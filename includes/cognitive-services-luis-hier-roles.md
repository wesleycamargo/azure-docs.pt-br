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
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208297"
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
