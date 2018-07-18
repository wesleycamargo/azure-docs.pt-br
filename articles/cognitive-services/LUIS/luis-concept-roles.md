---
title: Reconhecimento de como as funções são usadas em entidades baseadas em padrões – Azure | Microsoft Docs
description: Saiba como uma função é usada em uma entidade baseada em padrões para dar um nome a um subtipo de entidade contextual.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35364977"
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

```
buy a ticket from {Location:origin} to {Location:destination}
```

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
