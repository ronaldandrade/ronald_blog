---
author: Ronald Andrade
pubDatetime: 2023-11-23T22:35:00Z
title: Aplicando Python para mercado financeiro
postSlug: python-para-o-mercado-financeiro
featured: true
draft: false
tags:
  - python
description:
    Exemplo utilizando algumas das libs mais comuns em python para analisar dados e buscar informações da bolsa de valores em tempo real.
---

O Python deixou de ser apenas um diferencial e tornou-se um pré-requisito no cenário financeiro atual. Esta tecnologia avançada não é apenas o futuro; ela está aqui para facilitar diversas tarefas, desde a automação de rotinas até a obtenção e visualização de dados. No mercado financeiro, o Python se destaca como uma ferramenta incrível, com bibliotecas robustas, uma comunidade crescente e uma linguagem relativamente fácil de aprender. As dificuldades iniciais podem parecer desafiadoras, mas com paciência e consistência nos estudos, os benefícios são significativos.

## Table of contents

##  Automatizando Análises Financeiras com Python

Vamos começar instalando as bibliotecas necessárias. Abra seu terminal e digite:

```python
pip install yfinance numpy pandas matplotlib seaborn
```
Agora, importaremos as bibliotecas necessárias em nosso script Python:

```python
import numpy as np 
import pandas as pd 
import matplotlib.pyplot as plt 
import seaborn as sns
import yfinance as yf
```

## Construindo uma Carteira e Obtendo Dados em Tempo Real da B3

Imagine uma carteira fictícia composta por algumas ações. Vamos baixar os dados do IBOVESPA para os últimos 5 anos:

```python
tickers = "ABEV3.SA ITSA4.SA WEGE3.SA USIM5.SA VALE3.SA"

carteira = yf.download(tickers, period="5y")["Adj Close"]
ibov = yf.download("^BVSP", period="5y")["Adj Close"]
```

Vamos garantir que nossos dados estejam limpos, sem valores nulos:

```python
carteira.dropna(inplace=True)
ibov.dropna(inplace=True)
```

## Normalizando Dados para uma Comparação Justa

Normalizaremos os dados para que, no gráfico, todas as linhas partam do mesmo ponto:

```python
carteira_normalizada = (carteira / carteira.iloc[0]) * 10000
carteira_normalizada["saldo"] = carteira_normalizada.sum(axis=1)

ibov_normalizado = (ibov / ibov.iloc[0]) * 50000
```

## Visualização dos Resultados 

Agora, criaremos um gráfico para visualizar o desempenho da nossa carteira em comparação com o IBOVESPA:
```python
carteira_normalizada["saldo"].plot(figsize=(18,8), label="Minha Carteira")
ibov_normalizado.plot(label="IBOV")
plt.legend()
plt.show()
```

Ao executar este script, será possivel ter visualização clara do desempenho da sua carteira em relação ao índice IBOVESPA nos últimos 5 anos.

![gráfico](@assets/images/graficob3-v3.png)
