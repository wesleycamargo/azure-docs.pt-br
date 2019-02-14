---
title: Atributos de entidade de Instância de conferência ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Instância de Conferência na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879183"
---
# <a name="conference-instance-entity"></a>Entidade Instância de Conferência

<sub> *Os atributos a seguir são específicos da entidade de instância de conferência. (Ty = '4') </sub>

NOME    |DESCRIÇÃO                            |Type       | Operações
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID da Entidade                              |Int64      |É igual a
CIN     |Nome normalizado da instância de conferência ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Cadeia de caracteres     |É igual a
DCN     |Nome de exibição da instância da conferência ({ConferenceSeriesName} : {ConferenceInstanceYear})       |Cadeia de caracteres     |Nenhum
CIL     |Local da instância de conferência    |Cadeia de caracteres     |Igual a,<br/>StartsWith
CISD    |Data de início da instância de conferência  |Data       |Igual a,<br/>IsBetween
CIED    |Data de término da instância de conferência    |Data       |Igual a,<br/>IsBetween
CIARD   |Resumo da data de conclusão do registro da instância de conferência  |Data       |Igual a,<br/>IsBetween
CISDD   |Data de conclusão do envio da instância de conferência     |Data       |Igual a,<br/>IsBetween
CIFVD   |Data de conclusão da versão final da instância de conferência  |Data       |Igual a,<br/>IsBetween
CINDD   |Data de notificação da instância de conferência   |Data       |Igual a,<br/>IsBetween
CD.T    |Título de um evento de instância de conferência   |Data       |Igual a,<br/>IsBetween
CD.D    |Data de um evento de instância de conferência    |Data       |Igual a,<br/>IsBetween
PCS.CN  |Nome da série de conferências da instância |Cadeia de caracteres     |É igual a
PCS.CId |ID da série de conferências da instância |Int64    |É igual a
CC      |Contagem total de citações da instância de conferência           |Int32      |Nenhum  
ECC     |Contagem total estimada de citações da instância de conferência |Int32      |Nenhum


## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

NOME    | DESCRIÇÃO               
--------|---------------------------    
FN      | Nome completo da instância de conferência
