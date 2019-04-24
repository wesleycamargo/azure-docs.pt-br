---
title: Descrever conteúdo adulto e atrevido - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de conteúdo adulto e atrevido em imagens usando o APi Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368330"
---
# <a name="detect-adult-and-racy-content"></a>Detectar conteúdo adulto e erótico

A Pesquisa Visual Computacional pode detectar material adulto em imagens, de modo que os desenvolvedores podem restringir a exibição de tais imagens em seu software. Os sinalizadores de conteúdo são aplicados com uma pontuação entre zero e um para que os desenvolvedores possam interpretar os resultados de acordo com suas próprias preferências. 

> [!NOTE]
> Esse recurso também é oferecido pelo serviço [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview). Confira essa alternativa para soluções para cenários de moderação de conteúdo mais rigorosos, como a moderação de texto e fluxos de trabalho de análise humana.

## <a name="content-flag-definitions"></a>Definições de sinalizador de conteúdo

As imagens **Somente para adultos** são definidas como aquelas que são pornográficas por natureza e geralmente retratam nudez e atos sexuais. 

As imagens **obscenas** são definidas como imagens de natureza sexualmente sugestiva e, geralmente, contêm menos conteúdo sexualmente explícito do que imagens marcadas como **Somente para adultos**. 

## <a name="identify-adult-and-racy-content"></a>Identificar conteúdo adulto e erótico

A API [Analisar](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

O método Analyze Image retorna duas propriedades boolianas, `isAdultContent` e `isRacyContent`, na resposta JSON do método para indicar, conteúdo adulto e erótico, respectivamente. O método também retorna duas propriedades, `adultScore` e `racyScore`, que representam, as pontuações de confiança para identificar conteúdo adulto e erótico, respectivamente.

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [detectar conteúdo específico de domínio](concept-detecting-domain-content.md) e [detectar faces](concept-detecting-faces.md).
