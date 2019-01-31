---
title: Atributos da entidade Série de Conferências – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Série de Conferências.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155588"
---
# <a name="conference-series-entity"></a>Entidade Série de Conferências

<sub> *Os atributos a seguir são específicos da entidade da série de conferências. (Ty = '3') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
CN      |Nome normalizado da série de conferências      |Cadeia de caracteres     |É igual a
DCN     |Nome de exibição da série de conferências         |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da série de conferências         |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da série de conferências   |Int32      |Nenhum
F.FId   |ID da entidade do Campo de Estudo associado à série de conferências |Int64  | É igual a
F.FN    |Nome do Campo de Estudo associado à série de conferências  | Igual a,<br/>StartsWith
