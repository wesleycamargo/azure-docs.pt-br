---
title: Regiões – Serviços de Fala
titlesuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: 32a8b4cbfd0d8b43d294155432a0feaf29036c4c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218131"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do Serviço de Fala

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

Certifique-se de que você use o ponto de extremidade que corresponde à região de sua assinatura.

## <a name="speech-sdk"></a>SDK de fala

Na [SDK do serviço de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` na SDK de fala para C#).

### <a name="speech-recognition-and-translation"></a>Reconhecimento de Fala e tradução

O SDK DE fala está disponível nessas regiões para **reconhecimento de Fala** e **tradução**:

  Região | Parâmetro do SDK de Fala | Portal de personalização de Fala
 ------|-------|--------
 Oeste dos EUA | `westus` | https://westus.cris.ai
 Oeste dos EUA 2 | `westus2` | https://westus2.cris.ai
 Leste dos EUA | `eastus` | https://eastus.cris.ai
 Leste dos EUA 2 | `eastus2` | https://eastus2.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste da Ásia | `southeastasia` | https://southeastasia.cris.ai
 Norte da Europa | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

 Região global | Região | Parâmetro do SDK de Fala
 ------|-------|--------
 Ásia | Ásia Oriental | `eastasia`
 Ásia | Sudeste Asiático | `southeastasia`
 Austrália | Leste da Austrália | `australiaeast`
 Europa | Norte da Europa | `northeurope`
 Europa | Europa Ocidental | `westeurope`
 América do Norte | Leste dos EUA | `eastus`
 América do Norte | Leste dos EUA 2 | `eastus2`
 América do Norte | Centro-Sul dos Estados Unidos | `southcentralus`
 América do Norte | Centro-Oeste dos EUA | `westcentralus`
 América do Norte | Oeste dos EUA | `westus`
 América do Norte | Oeste dos EUA 2 | `westus2`
 América do Sul | Sul do Brasil | `brazilsouth`

Este é um subconjunto das regiões de publicação compatíveis com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala em texto, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de texto em fala, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
