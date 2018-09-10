---
title: O método Analisadores na API de Análise Linguística | Microsoft Docs
description: A API REST de analisadores fornece uma lista de analisadores que atualmente têm suporte pelo serviço nos Serviços Cognitivos da Microsoft.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363378"
---
# <a name="analyzers-method"></a>Método de analisadores

A API REST de **analisadores** fornece uma lista de analisadores atualmente com suporte pelo serviço.
A resposta inclui os [nomes](Analyzer-Names.md) e os idiomas com suporte em cada um (como "en" para inglês).

## <a name="request-parameters"></a>Parâmetros da solicitação
Nenhum

<br>

## <a name="response-parameters"></a>Parâmetros de resposta
NOME | type | DESCRIÇÃO
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
