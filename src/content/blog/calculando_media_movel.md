---
author: Ronald Andrade
pubDatetime: 2025-04-16T20:10:00Z
title: Visualizando Preços de Ações com Médias Móveis em Python
postSlug: Calculando Média Móvel
featured: true
draft: false
tags:
  - média movel
  - média
  - risco
description:
    Visualizando Preços de Ações com Médias Móveis em Python
---

Fala, galera! No post de hoje, vamos usar Python pra criar um gráfico que ajuda a visualizar os preços de uma ação e identificar tendências com médias móveis. Essa técnica é super prática pro dia a dia do investidor, porque suaviza o "ruído" do mercado e mostra se o preço tá subindo ou descendo. Vamos usar yfinance pra pegar os dados e matplotlib pra fazer um gráfico. Bora aprender?

**O que são Médias Móveis?** - As médias móveis são indicadores que calculam a média dos preços de fechamento de uma ação em um período específico (ex.: 10 dias, 50 dias). Elas ajudam a identificar tendências no mercado:

- **Média Móvel Simples (SMA)**: É a média aritmética dos preços em um período.

- **Cruzamento de Médias**: Quando a média de curto prazo (ex.: 10 dias) cruza a de longo prazo (ex.: 50 dias) pra cima, pode ser um sinal de compra. Se cruza pra baixo, pode indicar venda.

Aqui, vamos plotar os preços de uma ação da B3 e suas médias móveis de 10 e 50 dias.

## Table of contents

## Bibliotecas utilizadas

**[yfinance](https://pypi.org/project/yfinance/)**
**[matplotlib](https://matplotlib.org/)**


## Instalar as Bibliotecas
```bash
pip install yfinance matplotlib
```
## Código completo, com comentários pra você entender cada parte:

```python
import yfinance as yf
import matplotlib.pyplot as plt

# Definir o ticker da ação (ex.: PETR4 para Petrobras)
ticker = "PETR4.SA"

# Baixar dados dos últimos 6 meses
data = yf.download(ticker, period="6mo", interval="1d")

# Calcular médias móveis de 10 e 50 dias
data['SMA10'] = data['Close'].rolling(window=10).mean()
data['SMA50'] = data['Close'].rolling(window=50).mean()

# Criar o gráfico
plt.figure(figsize=(12, 6))
plt.plot(data['Close'], label='Preço de Fechamento', color='blue')
plt.plot(data['SMA10'], label='Média Móvel 10 Dias', color='orange')
plt.plot(data['SMA50'], label='Média Móvel 50 Dias', color='green')
plt.title(f'Preços e Médias Móveis de {ticker}')
plt.xlabel('Data')
plt.ylabel('Preço (R$)')
plt.legend()
plt.grid(True)
plt.savefig('grafico_petr4.png')  # Salva o gráfico como imagem
plt.show()

```

## Entendendo o Código

**yf.download**: Baixa os dados históricos da ação (preços de fechamento, abertura, etc.) do Yahoo Finance.

**rolling(window).mean()**: Calcula a média móvel. O window define o período (10 ou 50 dias).

**plt.plot**: Plota os preços e as médias móveis no mesmo gráfico.

**plt.savefig**: Salva o gráfico como uma imagem.

**plt.figure(figsize)**: Ajusta o tamanho do gráfico pra ficar mais legível. 


## Resultado
Ao rodar o código, você vai ver um gráfico com os preços de fechamento em azul, a média móvel de 10 dias em laranja e a de 50 dias em verde. Aqui tá um exemplo do que você vai gerar:

![gráfico](@assets/images/media_movel.png)

- Se a linha laranja (SMA10) cruzar a verde (SMA50) pra cima, pode ser um sinal de compra. Se cruzar pra baixo, pode indicar venda.

## Como Usar no Dia a Dia

**Identificar Tendências**: Se a média de 10 dias tá acima da de 50 dias, a ação pode estar em alta.

**Sinais de Compra/Venda**: Fique de olho nos cruzamentos das médias móveis pra tomar decisões.

**Customização**: Teste com outras ações (ex.: VALE3.SA, ITUB4.SA) ou períodos diferentes (ex.: 20 dias, 200 dias).

Com poucas linhas de Python, você consegue criar uma ferramenta visual poderosa pro investidor. As médias móveis são simples, mas ajudam a tomar decisões mais informadas. Lembrando que no mundo financeiro muitos fatores são relevantes e alguns são imprevisíveis, sendo difíceis de mensurar.