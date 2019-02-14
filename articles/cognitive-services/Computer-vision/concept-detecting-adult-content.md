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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 64db05e5e40b76d219ea0e3214c20297f32da4b5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861265"
---
# <a name="detecting-adult-and-racy-content"></a>Detectar conteúdo para adulto e obsceno

Entre as várias categorias visuais está o grupo de conteúdo para adulto, que permite a detecção de materiais para adulto e restringe a exibição de imagens que contêm conteúdo sexual. O filtro para detecção de conteúdo para adulto pode ser definido em uma escala deslizante para acomodar a preferência do usuário.

## <a name="defining-adult-and-racy-content"></a>Definindo conteúdo adulto e atrevido

Entre os vários recursos visuais cobertos pelo [método Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), o recurso visual Adulto permite a detecção de imagens adultas e atrevidas. Imagens de "Somente para adultos" são definidas como aqueles que são pornográficos por natureza e geralmente descrevem nudez e Atos sexuais. Imagens "atrevidas" são definidas como imagens de natureza sexualmente sugestiva e, geralmente, contêm menos conteúdo sexualmente explícito do que imagens marcadas como "Adulto". O tipo de recurso visual Adulto é comumente usado para restringir a exibição de imagens que contêm conteúdo sexualmente sugestivo e explicitamente sexual.

## <a name="identifying-adult-and-racy-content"></a>Identificação de conteúdo adulto e erótico

O método Analyze Image retorna duas propriedades, `isAdultContent` e `isRacyContent`, na resposta JSON do método para indicar, respectivamente, conteúdo adulto e vigoroso. Ambas as propriedades retornam um valor booleano, verdadeiro ou falso. O método também retorna duas propriedades, `adultScore` e `racyScore`, que representam, respectivamente, as pontuações de confiança para identificar conteúdo adulto e atrevido. Um filtro de confiança para detecção de conteúdo adulto e vigoroso pode ser definido em uma escala móvel para acomodar sua preferência com base em seu cenário específico.

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos sobre [detectar conteúdo específico de domínio](concept-detecting-domain-content.md) e [detectar faces](concept-detecting-faces.md).
