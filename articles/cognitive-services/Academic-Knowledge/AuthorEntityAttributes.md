---
title: Atributos de entidade de autor ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Autor na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175172"
---
# <a name="author-entity"></a>Entidade Autor
<sub> *Os atributos a seguir são específicos da entidade de autor. (Ty = '1') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
AuN     |Nome normalizado do autor                 |Cadeia de caracteres     |É igual a
DAuN    |Nome de exibição do autor                    |Cadeia de caracteres     |Nenhum
CC      |Contagem total de citações do autor            |Int32      |Nenhum  
ECC     |Contagem total estimada de citações do autor  |Int32      |Nenhum
E       |Metadados estendidos (consulte a tabela "Atributos Meta Estendidos")  |Cadeia de caracteres     |Nenhum  


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
LKA.Afn     | nome de exibição de afiliação associado ao autor  
LKA.AfId        | ID da entidade de afiliação associada ao autor
