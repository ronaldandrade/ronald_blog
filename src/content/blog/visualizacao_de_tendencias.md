---
author: Ronald Andrade
pubDatetime: 2025-01-28T17:10:00Z
title: Visualizando Tendências de Mercado com Matplotlib e Seaborn
postSlug: Visualizando Tendências de Mercado 
featured: true
draft: false
tags:
  - visualização
  - tendências de mercado
  - mercado financeiro
  - gráficos
description:
    Visualizando Tendências de Mercado criando gráficos com Matplotlib e Seaborn.
---
Neste post, vamos turbinar nossa análise de mercado com Matplotlib e Seaborn, criando gráficos pra visualizar tendências de ações da B3. Vamos construir um candlestick chart com médias móveis, destacando sinais de compra e venda, e um heatmap de correlação pra entender como diferentes ações se movem juntas. Usaremos yfinance pra pegar os dados e mplfinance pra gráficos financeiros. É uma ferramenta visual que todo investidor precisa pra tomar decisões rápidas. 

- **O que são essas visualizações?**

**Candlestick Chart**: Mostra preços de abertura, fechamento, máxima e mínima de uma ação, com médias móveis pra identificar tendências. Cruzamentos das médias podem indicar compra (pra cima) ou venda (pra baixo).



**Heatmap de Correlação**: Mostra como os retornos de várias ações estão correlacionados. Valores próximos de 1 indicam que as ações sobem ou descem juntas; próximos de -1, que se movem em direções opostas.

Aqui, vamos analisar a PETR4 e criar um candlestick com sinais de compra/venda, além de um heatmap com PETR4, VALE3, ITUB4 e BBDC4.

## Table of contents

## Bibliotecas utilizadas

**[Pandas](https://pandas.pydata.org/docs/)**

**[matplotlib](https://matplotlib.org/)**

**[seaborn](https://seaborn.pydata.org/)**

**[mplfinance](https://pypi.org/project/mpl-finance/)**

## Instalar as Bibliotecas

```bash
pip install yfinance matplotlib seaborn mplfinance
```
##  Código completo, com comentários pra você entender cada parte:

- Aqui, simplesmente importamos as bibliotecas usadas e escolhemos os *tickers* para criar o gráfico de comparação *heatmap*.

```python
import yfinance as yf
import matplotlib.pyplot as plt
import seaborn as sns
import mplfinance as mpf
import pandas as pd

# Definir tickers das ações
tickers = ["PETR4.SA", "VALE3.SA", "ITUB4.SA", "BBDC4.SA"]
```
- Agora vamos fazer o download dos dados que queremos trabalhar. Primeiro de todos os tikers escolhidos no período de 3 meses com fechamento diário, depois, somente da *PETR4* no período de 6 meses e também com fechamento diário.

```python
# Baixar dados dos últimos 3 meses (com fechamento diário)
data = yf.download(tickers, period="3mo", interval="1d")['Close']
returns = data.pct_change().dropna()

# Baixar dados de PETR4 para candlestick
petr4_data = yf.download("PETR4.SA", period="6mo", interval="1d")
```
- Aqui é uma simples correção de index, pois ao utilizar o função *donwload* do *yfinance* a tabela retorna com index dobrado para cada coluna.

```python
# Corrigir MultiIndex, se existir
if isinstance(petr4_data.columns, pd.MultiIndex):
    petr4_data.columns = petr4_data.columns.get_level_values(0)

# Selecionar colunas necessárias e limpar
required_columns = ['Open', 'High', 'Low', 'Close', 'Volume']
petr4_data = petr4_data[required_columns].dropna().copy()
petr4_data = petr4_data.astype(float)
```
- A seguir, faremos o cálculo de médias móveis com janelas de 10 e 50 dias.

```python
# Calcular médias móveis
petr4_data['SMA10'] = petr4_data['Close'].rolling(window=10).mean()
petr4_data['SMA50'] = petr4_data['Close'].rolling(window=50).mean()
```
- Criamos os sinais de alerta de compra e venda que serão sinalizados no gráfico de *candlestick*
```python

# Identificar sinais de compra e venda
sell_signals = (petr4_data['SMA10'] > petr4_data['SMA50']) & (petr4_data['SMA10'].shift(1) <= petr4_data['SMA50'].shift(1))
buy_signals = (petr4_data['SMA10'] < petr4_data['SMA50']) & (petr4_data['SMA10'].shift(1) >= petr4_data['SMA50'].shift(1))
```
- Isolamos os pontos para scatter com alinhamento correto e sem NaNs

```python
buy_points = petr4_data.loc[buy_signals, 'Close']
sell_points = petr4_data.loc[sell_signals, 'Close']
```

- Criamos a lista de elementos adicionais para o gráfico, com azul para compra e vermelho para venda.

```python
apds = [
    mpf.make_addplot(petr4_data['SMA10'], color='orange', label='SMA10'),
    mpf.make_addplot(petr4_data['SMA50'], color='green', label='SMA50')
]

if not buy_points.empty:
    buy_plot = pd.Series(index=petr4_data.index, dtype=float)
    buy_plot.loc[buy_points.index] = buy_points
    apds.append(
        mpf.make_addplot(buy_plot, type='scatter', markersize=100, marker='^', color='blue', label='Compra')
    )

if not sell_points.empty:
    sell_plot = pd.Series(index=petr4_data.index, dtype=float)
    sell_plot.loc[sell_points.index] = sell_points
    apds.append(
        mpf.make_addplot(sell_plot, type='scatter', markersize=100, marker='v', color='red', label='Venda')
    )
```

- E finalmente as configurações dos gráficos do nosso *candlestick* e *hitmap*
```python
# Plotar candlestick com médias móveis e sinais
mpf.plot(
    petr4_data,
    type='candle',
    addplot=apds,
    title='Candlestick PETR4',
    ylabel='Preço (R$)',
    style='yahoo',
    savefig='candlestick_petr4.png'
)

# Calcular e plotar a matriz de correlação dos retornos
corr_matrix = returns.corr()

plt.figure(figsize=(8, 6))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', vmin=-1, vmax=1, center=0)
plt.title('Correlação entre Retornos Diários das Ações')
plt.tight_layout()
plt.savefig('heatmap_correlacao.png')
plt.show()
```

## Uma explicação extra de cada trecho do código

- **yf.download**: Baixa dados históricos das ações. Para o heatmap, pega preços de fechamento de PETR4, VALE3, ITUB4 e BBDC4; para o candlestick, pega dados completos de PETR4 (abertura, fechamento, máxima, mínima, volume).

- **pct_change()**: Calcula os retornos diários das ações pra criar a matriz de correlação do heatmap.

- **MultiIndex check**: Verifica se o DataFrame tem colunas com MultiIndex (comum em dados do yfinance) e simplifica pra nomes simples (Open, High, etc.).

- **dropna()** e astype(float): Seleciona as colunas necessárias (Open, High, Low, Close, Volume), remove valores nulos e converte tudo pra float, garantindo compatibilidade com mplfinance.

- **rolling(window).mean()**: Calcula as médias móveis de 10 dias (SMA10) e 50 dias (SMA50) pra PETR4, usadas pra identificar tendências.

- **buy_signals/sell_signals**: Detecta cruzamentos das médias móveis. Compra ocorre quando SMA10 cruza SMA50 pra cima; venda, quando cruza pra baixo.

- **buy_points/sell_points**: Isola os preços de fechamento nos dias de sinais de compra/venda, garantindo que apenas pontos válidos sejam plotados.

- **pd.Series com NaN**: Cria séries para os sinais de compra/venda com o mesmo índice do petr4_data, preenchendo com NaN onde não há sinais, pra alinhar corretamente no gráfico.

- **mpf.make_addplot**: Adiciona médias móveis (linhas laranja e verde) e sinais de compra/venda (triângulos azuis e vermelhos) ao candlestick.

- **mpf.plot**: Gera o candlestick chart com velas (verdes pra alta, vermelhas pra baixa), médias móveis e sinais, salvando como imagem.

- **sns.heatmap**: Plota a matriz de correlação dos retornos, com cores indicando força e direção (vermelho pra correlação positiva, azul pra negativa), e salva como imagem.


## Resultado

Ao rodar o código, você vai gerar dois gráficos:

- *Candlestick Chart*: Mostra os preços de PETR4, com velas verdes (alta) e vermelhas (baixa), médias móveis (laranja e verde) e marcadores de compra (triângulos azuis) e venda (triângulos vermelhos):

![candlestick](@assets/images/candlestick_com_alerta.png)

# *Cuidado !*
-- Ao observar o gráfico, podemos notar que a compra vem na máxima e a venda na mínima. Para melhorar isso é preciso de mais ajustes, uma quantidade maior de dados, uma estratégia diferente para *identificar sinais de compra/venda*. Porém, o intuito desta postagem é mostrar a capacidade que a programação tem de nos ajudar, mas é preciso ter muito cuidado no que esta executando. -- 

- *Heatmap de Correlação*: Mostra a correlação entre os retornos de PETR4, VALE3, ITUB4 e BBDC4. Vermelho escuro indica alta correlação positiva; azul escuro, correlação negativa:

![candlestick](@assets/images/heatmap_de_carteira.png)


## Dicas de uso

- Identificar Tendências: Use o candlestick pra ver a direção do preço de ações e os sinais de compra/venda baseados nas médias móveis.

- Diversificar Carteira: O heatmap ajuda a escolher ações com baixa correlação (valores próximos de 0 ou negativos) pra reduzir o risco.

- Customização: Troque os tickers ou ajuste os períodos das médias móveis (ex.: 20 e 100 dias) pra testar outras estratégias.
