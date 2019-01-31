---
title: Atributos da entidade do Academic Graph ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos da entidade que você pode usar com o Grafo Acadêmico na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f227cc03578adcfbf73fec3ae8941045e8352513
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182992"
---
# <a name="entity-attributes"></a>Atributos da entidade

O grafo acadêmico é composto de 7 tipos de entidades. Todas as entidades terá uma ID de Entidade e um Tipo de Entidade.

## <a name="common-entity-attributes"></a>Atributos de entidades comuns
NOME    |DESCRIÇÃO                |Type       | Operações
------- | ------------------------- | --------- | ----------------------------
ID      |ID da Entidade                  |Int64      |É igual a
Ty      |Tipo de entidade                |enum   |É igual a

## <a name="entity-type-enum"></a>Enumeração de tipo de entidade
NOME                                                            |value
----------------------------------------------------------------|-----
[Papel](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Diário](JournalEntityAttributes.md)                           |2
[Série de conferência](JournalEntityAttributes.md)                 |3
[Instância de conferência](ConferenceInstanceEntityAttributes.md)    |4
[Afiliação](AffiliationEntityAttributes.md)                   |5
[Campo de estudo](FieldsOfStudyEntityAttributes.md)                      |6

