---
title: Atributos de entidade de autor ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Autor na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900677"
---
# <a name="author-entity"></a>Entidade Autor
<sub> *Os atributos a seguir são específicos da entidade de autor. (Ty = '1') </sub>

NOME    |DESCRIÇÃO                            |Tipo       | Operações
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