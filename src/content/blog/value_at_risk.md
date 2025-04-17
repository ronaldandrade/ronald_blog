---
author: Ronald Andrade
pubDatetime: 2025-01-28T17:10:00Z
title: Calculando o Value at Risk (VaR) de uma Ação com Python
postSlug: Calculando VaR 
featured: true
draft: false
tags:
  - value at risk  
  - risco
  - finanças
  - investimento
description:
    Calculando o Value at Risk (VaR) de uma Ação com Python 
---

Neste post,quero mostrar uma ferramenta essencial pro investidor: o Value at Risk (VaR). O VaR é uma métrica que estima a perda máxima que uma ação (ou carteira) pode ter em um período, com um certo nível de confiança. Vamos usar Python pra calcular o VaR histórico de uma ação da B3, com as bibliotecas yfinance pra pegar dados e numpy pra fazer as contas. É uma técnica prática pra quem quer gerenciar riscos na carteira.

- O que é o Value at Risk (VaR)?

O VaR mede o quanto você pode perder (em reais) em um investimento, considerando um intervalo de confiança (ex.: 95%) e um horizonte de tempo (ex.: 1 dia). Por exemplo, um VaR de R$ 100 a 95% significa que, em 95% dos dias, sua perda não vai ultrapassar R$ 100. Vamos calcular o VaR histórico, que usa os retornos passados da ação pra estimar o risco.

Nos próximos tópicos, vamos calcular o VaR diário de uma ação da B3, com 95% de confiança, usando os últimos 6 meses de dados.

## Table of contents

## Bibliotecas utilizadas

**[Pandas](https://pandas.pydata.org/docs/)**

**[matplotlib](https://matplotlib.org/)**


## Instalando as bibliotecas
```bash
pip install yfinance numpy matplotlib
```

## Código completo, comentado em cada parte para fácil compreenssão:

```python
import yfinance as yf
import numpy as np
import matplotlib.pyplot as plt

# Definir o ticker da ação (ex.: VALE3 para Vale)
ticker = "VALE3.SA"

# Baixar dados dos últimos 6 meses
data = yf.download(ticker, period="6mo", interval="1d")

# Calcular retornos diários
data['Returns'] = data['Close'].pct_change().dropna()

# Parâmetros do VaR
confidence_level = 0.95  # 95% de confiança
investment = 10000  # Investimento de R$ 10.000

# Calcular VaR histórico
returns = data['Returns'].dropna()
var_percentile = np.percentile(returns, 100 * (1 - confidence_level))
var_value = investment * var_percentile

# Exibir resultado
print(f"VaR a {confidence_level*100}% de confiança: R$ {var_value:.2f}")

# Plotar histograma dos retornos
plt.figure(figsize=(10, 6))
plt.hist(returns, bins=50, alpha=0.7, color='blue')
plt.axvline(var_percentile, color='red', linestyle='--', label=f'VaR ({confidence_level*100}%)')
plt.title(f'Histograma de Retornos Diários de {ticker}')
plt.xlabel('Retorno Diário')
plt.ylabel('Frequência')
plt.legend()
plt.grid(True)
plt.savefig('histograma_var.png')  # Salva o gráfico como imagem
plt.show()
```
```python
VaR a 95.0% de confiança: R$ -251.31
```

## Explicando o Código

- **yf.download**: Baixa os dados históricos da ação (preços de fechamento, etc.) do Yahoo Finance.

- **pct_change()**: Calcula os retornos diários da ação (variação percentual do preço).

- **np.percentile**: Calcula o percentil dos retornos correspondente ao nível de confiança (5% para 95% de confiança).

- **investment * var_percentile**: Converte o VaR percentual em valor monetário, baseado no investimento.

- **plt.hist**: Plota um histograma dos retornos, com a linha do VaR marcada em vermelho.

- **plt.savefig**: Salva o gráfico como uma imagem.

## Conclusão
Ao rodar o código, você vai ver o **VaR calculado** (ex.: "VaR a 95% de confiança: R$ -250.00") e um histograma dos retornos diários, com uma linha vermelha mostrando o VaR. O gráfico a seguir: 

![gráfico](@assets/images/value_at_risk.png)

O VaR negativo indica a perda máxima esperada. Por exemplo, se o VaR for R$ -250, significa que, em 95% dos dias, você não vai perder mais que R$ 250 em um investimento de R$ 10.000.

## Como aplicar no Dia a Dia

- **Gerenciar** Risco: Use o VaR pra saber o risco máximo do seu investimento e ajustar sua estratégia.

- **Comparar Ativos**: Calcule o VaR de diferentes ações (ex.: BBSA3.SA, ITUB4.SA) pra escolher as menos arriscadas.

- **Customização**: Ajuste o **confidence_level** (ex.: 99%) ou o período de dados (ex.: 1 ano) pra análises mais conservadoras ou longas.

Com poucas linhas de Python, você tem uma ótima ferramenta pra medir o risco de uma ação. O VaR é simples, mas ajuda a tomar decisões mais seguras, principalmente se utilizado junto a outros medidores. Lembrando que no mundo financeiro muitos fatores são relevantes e alguns são imprevisíveis, sendo difíceis de mensurar.