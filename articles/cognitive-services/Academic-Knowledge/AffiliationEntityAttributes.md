---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Afiliação na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190610"
---
# <a name="affiliation-entity"></a>Entidade Afiliação

<sub> *Os atributos a seguir são específicos da entidade de afiliação. (Ty = '5') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
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
