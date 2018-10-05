---
title: Reconhecimento de como as funções são usadas em entidades baseadas em padrões
titleSuffix: Azure Cognitive Services
description: As funções são subtipos nomeados e contextuais de uma entidade usada apenas em padrões. Por exemplo, no enunciado “compre uma passagem de Nova York para Londres” Nova York e Londres são cidades, mas cada uma tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035191"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>As funções de entidade em Padrões são subtipos contextuais
As funções são subtipos nomeados, contextuais de uma entidade usada apenas em [padrões](luis-concept-patterns.md).

Por exemplo, na declaração `buy a ticket from New York to London`, New York e London são cidades, mas cada uma tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino. 

As funções dão um nome para essas diferenças:

|Entidade|Função|Finalidade|
|--|--|--|
|Local padrão|origin|de onde o avião parte|
|Local padrão|destino|onde o avião pousa|

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

## <a name="next-steps"></a>Próximas etapas

* Saiba como adicionar [funções](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
