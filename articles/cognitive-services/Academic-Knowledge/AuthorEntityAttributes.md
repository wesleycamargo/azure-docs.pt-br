---
title: Atributos de entidade Autor na API de Conhecimento Acadêmico | Microsoft Docs
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Autor na API de Conhecimento Acadêmico em Serviços Cognitivos.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363342"
---
# <a name="author-entity"></a>Entidade Autor
<sub> *Os atributos a seguir são específicos da entidade de autor. (Ty = '1') </sub>

NOME    |DESCRIÇÃO                            |type       | Operações
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