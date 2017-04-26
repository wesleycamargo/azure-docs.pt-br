---
title: "Práticas recomendadas para lidar com erros nos Hubs de Eventos do Azure | Microsoft Docs"
description: Lidar com erros e tentar novamente nos Hubs de Eventos do Azure
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>Práticas recomendadas para lidar com erros e tentar novamente nos Hubs de Eventos do 🔧

> [!NOTE]
> 
> Este tópico ainda não foi escrito! 
>
> Sua contribuição é bem-vinda para ajudar a definir o escopo e a abordagem deste conteúdo. É possível acompanhar o status e fornecer comentários sobre esse [tema no GitHub](https://github.com/Azure/azure-event-hubs/issues/305).
> 
> Saiba mais sobre como você pode contribuir no [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md).

Este artigo abordará:

- Quais são os tipos diferentes de erros que existem?
- O que acontece quando eu recebo um erro?
- Com quais tipos de erros devo tentar novamente? Com quais não devo?
- Política de repetição personalizada

