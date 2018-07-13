---
title: Referência de API para o serviço de API de Detecção Facial | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: A referência da API fornece informações sobre o Gerenciamento de Pessoa, o Gerenciamento de LargePersonGroup/PersonGroup, o Gerenciamento de LargeFaceList/FaceList e APIs de Algoritmos de Face.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 94c03d4f07a18c0c63d76965b1b31dce66388843
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364686"
---
# <a name="api-reference"></a>Referência de API

A API de Detecção Facial da Microsoft é uma API baseada em nuvem que fornece os algoritmos mais avançados para reconhecimento e detecção faciais.

As APIs de Detecção Facial cobrem as seguintes categorias:

- [APIs de Algoritmo de Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236): cobre funções centrais, como [Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Localizar Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a), [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) e [Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
- [APIs de Gerenciamento de FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b): usadas para gerenciar uma FaceList para [Localizar Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).
- [APIs de Gerenciamento de Pessoa de LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40): usada para gerenciar Faces de Pessoa de LargePersonGroup para [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [APIs de Gerenciamento de LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d): usadas para gerenciar um conjunto de dados de LargePersonGroup para [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [APIs de Gerenciamento de LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc): usadas para gerenciar uma FaceList para [Localizar Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).
- [APIs de Gerenciamento de Pessoa de PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c): usada para gerenciar Faces de Pessoa de PersonGroup para [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
- [APIs de Gerenciamento de PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244): usadas para gerenciar um conjunto de dados de PersonGroup para [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
