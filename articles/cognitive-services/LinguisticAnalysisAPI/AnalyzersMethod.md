---
title: O método Analisadores ‒ API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: A API REST de analisadores fornece uma lista de analisadores que atualmente têm suporte na API de Análise Linguística.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 9338e87644554ac8b3121c5341cea6f2bc512a97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878180"
---
# <a name="analyzers-method"></a>Método de analisadores

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

A API REST de **analisadores** fornece uma lista de analisadores atualmente com suporte pelo serviço.
A resposta inclui os [nomes](Analyzer-Names.md) e os idiomas com suporte em cada um (como "en" para inglês).

## <a name="request-parameters"></a>Parâmetros da solicitação
Nenhum

<br>

## <a name="response-parameters"></a>Parâmetros de resposta
NOME | Type | DESCRIÇÃO
-----|------|--------------
idiomas | lista de cadeias de caracteres | lista de códigos de idioma ISO de duas letras para os quais esse analisador pode ser usado.
ID   | string | ID exclusiva desse analisador
kind | string | aqui, o tipo de analisador amplo
especificação | string | o nome da especificação usada para esse analisador
implementação | string | descrição do modelo e/ou algoritmo desse analisador

<br>
## <a name="example"></a>Exemplo
GET /analisadores

Resposta: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
