---
title: Atributos de entidade de diário ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Diário na API de Conhecimento Acadêmico em Serviços Cognitivos.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902800"
---
# <a name="journal-entity"></a>Entidade Diário

<sub> *Os atributos a seguir são específicos da entidade de diário. (Ty = '2') </sub>

NOME    |DESCRIÇÃO                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
DJN     |Nome normalizado do diário                |Cadeia de caracteres     |Nenhum
JN      |Nome de exibição do diário                   |Cadeia de caracteres     |É igual a
CC      |Contagem total de citações do diário           |Int32      |Nenhum  
ECC     |Contagem total estimada de citações do diário |Int32      |Nenhum