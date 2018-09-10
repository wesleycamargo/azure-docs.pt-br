---
title: Atributos de entidade Campo de Estudo na API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Campo de Estudo na API de Conhecimento Acadêmico nos Serviços cognitivos.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363344"
---
# <a name="field-of-study-entity"></a>Entidade Campo de Estudo

<sub> *Os atributos a seguir são específicos para a entidade do campo de estudo. (Ty = '6') </sub>

NOME    |DESCRIÇÃO                            |type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
FN      |Nome normalizado do campo de estudo         |Cadeia de caracteres     |É igual a
DFN     |Nome de exibição do campo de estudo            |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações do campo de estudo    |Int32      |Nenhum  
ECC     |Campo da contagem total estimada de citações|Int32      |Nenhum
FL      |Nível nos campos de hierarquia de estudo     |Int32      |É igual a, <br/>IsBetween
FP.FN   |Nome do campo de estudo pai             |Cadeia de caracteres     |É igual a
FP.FId  |ID do campo de estudo pai               |Int64      |É igual a
FC.FN   |Nome do campo de estudo filho              |Cadeia de caracteres     |É igual a
FC.FId  |ID do campo de estudo filho                |Int64      |É igual a