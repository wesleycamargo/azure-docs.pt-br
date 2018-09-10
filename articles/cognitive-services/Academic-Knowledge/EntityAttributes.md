---
title: Atributos da entidade Grafo Acadêmico para a API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos da entidade que você pode usar com o Grafo Acadêmico na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363340"
---
# <a name="entity-attributes"></a>Atributos da entidade

O grafo acadêmico é composto de 7 tipos de entidades. Todas as entidades terá uma ID de Entidade e um Tipo de Entidade.

## <a name="common-entity-attributes"></a>Atributos de entidades comuns
NOME    |DESCRIÇÃO                |type       | Operações
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

