---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: dee31fca841ea309128681637cc41fa0fb1e7aea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480651"
---
## <a name="roles-versus-hierarchical-entities"></a>Papéis versus entidades hierárquicas

Você deve usar uma entidade hierárquica ou um padrão com uma entidade simples com funções? 

Isso depende. Os padrões e as declarações de exemplo são comparáveis na medida em que representam uma declaração de usuário e são específicos para uma intenção.  

Se as declarações não tiverem um padrão claro, use as entidades hierárquicas. 

|Entidades hierárquicas|As entidade simples com funções|
|--|--|
|Disponível no exemplo de declarações e padrões de intenção|Apenas disponível em padrões|
|Deve ter exemplos de declarações em intenção com entidades filhas rotuladas|Os papéis não podem ser rotulados em declarações de exemplo em intenção|
|Pode precisar de **mais** exemplos de declarações na intenção de extrair entidade|Deve precisar de **menos** exemplos de declarações de intenção|
