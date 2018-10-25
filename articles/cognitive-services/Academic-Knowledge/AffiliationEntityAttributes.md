---
title: Atributos de entidade de afiliação na API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Afiliação na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900471"
---
# <a name="affiliation-entity"></a>Entidade Afiliação

<sub> *Os atributos a seguir são específicos da entidade de afiliação. (Ty = '5') </sub>

NOME    |DESCRIÇÃO                            |Tipo       | Operações
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