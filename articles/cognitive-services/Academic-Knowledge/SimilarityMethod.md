---
title: Método de similaridade – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o Método de similaridade para calcular a similaridade acadêmica de duas cadeias de caracteres.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 76e86eb78a06d98e3d5c6c54b244add3c0c245d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900454"
---
# <a name="similarity-method"></a>Método de Similaridade

A API REST de **similaridade** é usada para calcular a similaridade acadêmica entre duas cadeia de caracteres. 
<br>

**Ponto de extremidade REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parâmetros de solicitação
Parâmetro        |Tipo de Dados      |Obrigatório | DESCRIÇÃO
----------|----------|----------|------------
**s1**        |Cadeia de caracteres   |SIM  |Cadeia de caracteres * a ser comparada
**s2**        |Cadeia de caracteres   |SIM  |Cadeia de caracteres * a ser comparada
<sub> *As cadeia de caracteres a serem comparadas têm um comprimento máximo de 1MB.</sub>
<br>
## <a name="response"></a>Response
NOME | DESCRIÇÃO
--------|---------
**SimilarityScore**        |Um valor de ponto flutuante representando a similaridade do cosseno de s1 e s2, com valores mais próximos a 1,0, significando mais semelhante e valores mais próximos a -1,0, significando menos semelhante
<br>

## <a name="successerror-conditions"></a>Condições de erro/êxito
Status HTTP | Motivo | Response
-----------|----------|--------
**200**         |Sucesso | Número de ponto flutuante
**400**         | Solicitação incorreta ou solicitação inválida | Mensagem de erro      
**500**         |Erro interno do servidor | Mensagem de erro
**Tempo Limite**     | Atingiu tempo limite solicitado.  | Mensagem de erro
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exemplo: calcule a similaridade de dois abstratos parciais
#### <a name="request"></a>Solicitação:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Neste exemplo, geramos a pontuação de similaridade entre dois abstratos parciais usando a API de **similaridade**.
#### <a name="response"></a>Resposta:
```
0.520
```
#### <a name="remarks"></a>Comentários:
A pontuação de similaridade é determinada pela avaliação dos conceitos acadêmicos por meio da incorporação de palavras. Neste exemplo, 0,52 significa que os dois abstratos parciais são um pouco semelhantes.
<br>