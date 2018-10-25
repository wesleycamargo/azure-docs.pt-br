---
title: Reconhecimento de como as funções são usadas em entidades baseadas em padrões
titleSuffix: Azure Cognitive Services
description: As funções são subtipos nomeados e contextuais de uma entidade usada apenas em padrões. Por exemplo, no enunciado “compre uma passagem de Nova York para Londres” Nova York e Londres são cidades, mas cada uma tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9bbbb797cd7e7d1cea52f1d5b1b491998b595db7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638064"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>As funções de entidade em Padrões são subtipos contextuais
As funções são subtipos nomeados, contextuais de uma entidade usada apenas em [padrões](luis-concept-patterns.md).

Por exemplo, na declaração `buy a ticket from New York to London`, New York e London são cidades, mas cada uma tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino. 

As funções dão um nome para essas diferenças:

|Entidade|Função|Finalidade|
|--|--|--|
|Local padrão|origin|de onde o avião parte|
|Local padrão|destino|onde o avião pousa|
|datetimeV2 predefinido|para|Data de término|
|datetimeV2 predefinido|de|Data de início|

## <a name="how-are-roles-used-in-patterns"></a>Como as funções são usadas em padrões?
Na declaração modelo do padrão, as funções são usadas dentro da declaração: 

|Padrão com funções de entidade|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintaxe da função em padrões
A entidade e a função estão entre parênteses, `{}`. A entidade e a função são separadas por dois-pontos. 

## <a name="roles-versus-hierarchical-entities"></a>Funções versões entidades hierárquicas
As entidades hierárquicas fornecem as mesmas informações contextuais, como funções, mas apenas para as declarações em **intenções**. Da mesma forma, as funções fornecem as mesmas informações contextuais que as entidades hierárquicas, mas apenas em **padrões**.

|Aprendizado contextual|Usado em|
|--|--|
|entidades hierárquicas|intenções|
|roles|padrões|

## <a name="roles-with-prebuilt-entities"></a>Funções com entidades pré-construídas

Use funções com entidades pré-construídas para dar significado a diferentes instâncias da entidade pré-construída em um enunciado. 

### <a name="roles-with-datetimev2"></a>Funções com datetimeV2

A entidade predefinida, datetimeV2, faz um ótimo trabalho compreender uma ampla variedade de variedade em datas e horas em declarações. Você talvez queira especificar datas e intervalos de datas, diferentemente da compreensão de padrão da entidade predefinidas. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como adicionar [funções](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
