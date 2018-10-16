---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164580"
---
**Pergunta**: você deve usar uma entidade hierárquica ou um padrão com uma entidade simples com funções? 

**Resposta**: depende. Os padrões e as declarações de exemplo são comparáveis na medida em que representam uma declaração de usuário e são específicos para uma intenção.  

Se as declarações não tiverem um padrão claro, use as entidades hierárquicas. 

|Entidades hierárquicas|As entidade simples com funções|
|--|--|
|devem ter declarações de exemplo com entidades filho rotuladas em intenções|devem ter declarações de exemplo, **as funções não podem ser rotuladas em intenções**|
|podem ser usadas em padrões|**devem** ser usadas em padrões|
|podem precisar de **mais** declarações de exemplo na intenção|podem precisar de **menos declarações de exemplo na intenção|