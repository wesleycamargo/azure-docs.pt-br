---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Afiliação na API de Conhecimento Acadêmico em Serviços Cognitivos.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363341"
---
# <a name="affiliation-entity"></a>Entidade Afiliação

<sub> *Os atributos a seguir são específicos da entidade de afiliação. (Ty = '5') </sub>

NOME    |DESCRIÇÃO                            |type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
AfN     |Nome normalizado da afiliação        |Cadeia de caracteres     |É igual a
DAfN    |Nome de exibição da afiliação       |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações da afiliação           |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da afiliação |Int32      |Nenhum

## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
Computador      |Contagem de artigos da afiliação