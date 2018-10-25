---
title: Atributos da entidade do Academic Graph ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos da entidade que você pode usar com o Grafo Acadêmico na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902783"
---
# <a name="entity-attributes"></a>Atributos da entidade

O grafo acadêmico é composto de 7 tipos de entidades. Todas as entidades terá uma ID de Entidade e um Tipo de Entidade.

## <a name="common-entity-attributes"></a>Atributos de entidades comuns
NOME    |DESCRIÇÃO                |Tipo       | Operações
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

