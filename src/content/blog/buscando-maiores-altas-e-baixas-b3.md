---
author: Ronald Andrade
pubDatetime: 2023-12-02T22:10:00Z
title: Explorando as Maiores Altas e Baixas na Bolsa de Valores com Python
postSlug: explorando api b3
featured: true
draft: false
tags:
  - python
  - b3
  - API
description:
    Utilizaremos a API da B3 e Python para para buscar as maiores altas e baixas do dia. 
---

Em um mundo financeiro dinâmico, estar ciente das maiores altas e baixas do dia é essencial para qualquer investidor. Neste tutorial, vamos explorar como utilizar a API da B3, a bolsa de valores brasileira, para buscar informações sobre as principais variações de preços das ações. Vamos criar um script em Python que nos permitirá acessar dados em tempo real e gerar um relatório simples e informativo sobre as maiores altas e baixas do dia.

## Table of contents

## Bibliotecas utilizadas

**[Requests](https://pypi.org/project/requests/)**

**[Pandas](https://pandas.pydata.org/docs/)**

## Onde extrair a API da [B3](https://www.b3.com.br/pt_br/market-data-e-indices/servicos-de-dados/market-data/cotacoes/)
Após acessar o link a cima, vá em `inspecionar página(F12)` e no arquivo javascript ,que pode ser encontrado no `debbuger`,
é só procurar pelo link da API como na imagem a baixo.

![gráfico](@assets/images/apib3.png)


##  Script para Buscar Maiores Altas e Baixas na B3: Explicação Detalhada

### Import das bibliotecas e link da API
Começaremos com a importação das bibliotecas necessárias: requests para fazer solicitações HTTP e pandas para manipulação eficiente dos dados. Em seguida, definimos a URL da API da B3 destinada a fornecer informações sobre as variações de preço do índice Ibovespa.


```python
import requests
import pandas as pd

url = 'https://cotacao.b3.com.br/mds/api/v1/InstrumentPriceFluctuation/ibov'
response = requests.get(url)
topGainers = response.json()
```
Aqui, fazemos uma solicitação GET à API da B3, que retorna em formato JSON e armazenamos as informações sobre as maiores altas e baixas em `topGainers`.

A seguir, criamos duas classes, `getTopGainers` e `getTopLosers`, para organizar as informações sobre as maiores altas e baixas.

### Classes para organizar as ações e os preços
```python
class getTopGainers:
topGainersDay = { 
'tikers' : [ topGainers['SctyHghstIncrLst'][0]['symb'],
topGainers['SctyHghstIncrLst'][1]['symb'],
topGainers['SctyHghstIncrLst'][2]['symb'], 
topGainers['SctyHghstIncrLst'][3]['symb'],
topGainers['SctyHghstIncrLst'][4]['symb']
],
'prices' : [ topGainers['SctyHghstIncrLst'][0]['SctyQtn']['curPrc'],
topGainers['SctyHghstIncrLst'][1]['SctyQtn']['curPrc'],
topGainers['SctyHghstIncrLst'][2]['SctyQtn']['curPrc'],
topGainers['SctyHghstIncrLst'][3]['SctyQtn']['curPrc'],
topGainers['SctyHghstIncrLst'][4]['SctyQtn']['curPrc']
]
} 
class getTopLosers:
topLosersDay = {
'tikers' : [
topGainers['SctyHghstDrpLst'][0]['symb'],
topGainers['SctyHghstDrpLst'][1]['symb'],
topGainers['SctyHghstDrpLst'][2]['symb'], 
topGainers['SctyHghstDrpLst'][3]['symb'],
topGainers['SctyHghstDrpLst'][4]['symb']
],
'price' : [
topGainers['SctyHghstDrpLst'][0]['SctyQtn']['curPrc'],
topGainers['SctyHghstDrpLst'][1]['SctyQtn']['curPrc'],
topGainers['SctyHghstDrpLst'][2]['SctyQtn']['curPrc'],
topGainers['SctyHghstDrpLst'][3]['SctyQtn']['curPrc'],
topGainers['SctyHghstDrpLst'][4]['SctyQtn']['curPrc']
]
}
```
Dentro dessas classes, extraímos os símbolos(tickers) e os preços das ações correspondentes.

### Função que gera o relatório  
Por fim, definimos uma função `gerar_relatorio` que recebe instâncias das classes `getTopGainers` e `getTopLosers`, organiza os dados em DataFrames do Pandas e os mescla em um relatório consolidado.

```python
def gerar_relatorio(top_gainers, top_losers):
top_gainers_data = {
'Ação': top_gainers.topGainersDay['tikers'],
'Preço': top_gainers.topGainersDay['prices']
}
top_losers_data = {
'Ação': top_losers.topLosersDay['tikers'],
'Preço': top_losers.topLosersDay['price']
}

top_gainers_df = pd.DataFrame(top_gainers_data)
top_losers_df = pd.DataFrame(top_losers_data)
merged_df = pd.concat([top_gainers_df, top_losers_df], keys=['Maiores Altas', 'Maiores Baixas'])
rel = merged_df.to_string('relatorio.txt')

return rel


top_gainers = getTopGainers()
top_losers = getTopLosers()

rel_result = gerar_relatorio(top_gainers, top_losers)
```

Este script não apenas nos dá uma visão clara das maiores altas e baixas, mas também destaca a flexibilidade e poder de Python na manipulação de dados financeiros em tempo real. Experimente executar o script para obter insights valiosos sobre o mercado de ações brasileiro.

