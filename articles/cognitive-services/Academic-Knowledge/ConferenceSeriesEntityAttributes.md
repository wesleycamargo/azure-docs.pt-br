---
title: Atributos da entidade Série de Conferências na API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Série de Conferências em Serviços Cognitivos.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363345"
---
# <a name="conference-series-entity"></a>Entidade Série de Conferências

<sub> *Os atributos a seguir são específicos da entidade da série de conferências. (Ty = '3') </sub>

NOME    |DESCRIÇÃO                            |type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
CN      |Nome normalizado da série de conferências      |Cadeia de caracteres     |É igual a
DCN     |Nome de exibição da série de conferências         |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da série de conferências         |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da série de conferências   |Int32      |Nenhum
F.FId   |ID da entidade do Campo de Estudo associado à série de conferências |Int64  | É igual a
F.FN    |Nome do Campo de Estudo associado à série de conferências  | Igual a,<br/>StartsWith