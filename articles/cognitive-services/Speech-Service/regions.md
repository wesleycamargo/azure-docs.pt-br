---
title: Fala de regiões de serviço
description: Referência para regiões do serviço de fala.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.component: speech
ms.topic: article
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 01c76d80d6b2fd64165b126df01c391d7e18292f
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887540"
---
# <a name="regions-of-the-speech-service"></a>Regiões do serviço de Fala

O serviço de Fala está disponível em diferentes regiões.
Quando você cria uma assinatura, você pode selecionar uma região disponível com base em suas necessidades.

Quando você usar a assinatura, deverá levar em conta a região selecionada.

## <a name="rest-api"></a>API REST

Use a API REST para selecionar os pontos de extremidade específicos da região corretos.
Consulte [APIs REST](rest-apis.md) para obter mais detalhes.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regiões para o reconhecimento de fala e tradução

A tabela abaixo lista as regiões disponíveis para **reconhecimento de fala** e **tradução**.

  Região | Parâmetro do SDK de Fala | Portal
 ------|-------|--------
 Oeste dos EUA | `westus` | https://westus.cris.ai
 Oeste dos EUA 2 | `westus2` | https://westus2.cris.ai 
 Leste dos EUA | `eastus` | https://eastus.cris.ai
 Leste dos EUA 2 | `eastus2` | https://eastus2.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste da Ásia | `southeastasia` | https://southeastasia.cris.ai
 Norte da Europa | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Amostra para reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala estão listadas na [página da região do serviço da API de Reconhecimento Vocal](/azure/cognitive-services/luis/luis-reference-regions).
Para cada região de publicação listada, o parâmetro da região do SDK de Fala correspondente é determinado como a primeira parte do nome de domínio do ponto de extremidade.
Por exemplo, use `westus` para especificar a região de publicação Oeste dos EUA.
