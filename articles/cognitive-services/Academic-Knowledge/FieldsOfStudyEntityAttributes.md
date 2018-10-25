---
title: Atributos da entidade Campo de Estudo ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Campo de Estudo na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900420"
---
# <a name="field-of-study-entity"></a>Entidade Campo de Estudo

<sub> *Os atributos a seguir são específicos para a entidade do campo de estudo. (Ty = '6') </sub>

NOME    |DESCRIÇÃO                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
FN      |Nome normalizado do campo de estudo         |Cadeia de caracteres     |É igual a
DFN     |Nome de exibição do campo de estudo            |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações do campo de estudo    |Int32      |Nenhum  
ECC     |Campo da contagem total estimada de citações|Int32      |Nenhum
FL      |Nível nos campos de hierarquia de estudo     |Int32      |Igual a, <br/>IsBetween
FP.FN   |Nome do campo de estudo pai             |Cadeia de caracteres     |É igual a
FP.FId  |ID do campo de estudo pai               |Int64      |É igual a
FC.FN   |Nome do campo de estudo filho              |Cadeia de caracteres     |É igual a
FC.FId  |ID do campo de estudo filho                |Int64      |É igual a