---
title: Atributos da entidade Série de Conferências – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Série de Conferências.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900741"
---
# <a name="conference-series-entity"></a>Entidade Série de Conferências

<sub> *Os atributos a seguir são específicos da entidade da série de conferências. (Ty = '3') </sub>

NOME    |DESCRIÇÃO                            |Tipo       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
CN      |Nome normalizado da série de conferências      |Cadeia de caracteres     |É igual a
DCN     |Nome de exibição da série de conferências         |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da série de conferências         |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da série de conferências   |Int32      |Nenhum
F.FId   |ID da entidade do Campo de Estudo associado à série de conferências |Int64  | É igual a
F.FN    |Nome do Campo de Estudo associado à série de conferências  | Igual a,<br/>StartsWith