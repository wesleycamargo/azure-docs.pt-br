---
title: Regiões do Serviço de Fala
titlesuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8d36036332e939075ffac8763bec9c23d8e4a3f7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712884"
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

**Reconhecimento de intenção** para o SKD de Fala compartilha suporte de regiões com LUIS. Para obter uma lista completa das regiões disponíveis, consulte [Publicar regiões e ponto de extremidade - LUIS](https://docs.microsoft.comazure/cognitive-services/luis/luis-reference-regions)

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala estão listadas na [página da região do serviço da API de Reconhecimento Vocal](/azure/cognitive-services/luis/luis-reference-regions).

Para cada região de publicação listada, use o **nome da região API**. Por exemplo, use `westus` para Oeste dos EUA.

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala em texto, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de texto em fala, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
