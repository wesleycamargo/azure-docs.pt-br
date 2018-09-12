---
title: Fala de regiões de serviço
description: Referência para regiões do serviço de fala.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379563"
---
# <a name="regions-of-the-speech-service"></a>Regiões do serviço de Fala

O serviço de Fala está disponível em diferentes regiões.
Ao criar uma assinatura você poderá escolher uma região disponível, dependendo de suas necessidades.

Quando você usar a assinatura, deverá levar em conta a região escolhida.

## <a name="rest-api"></a>API REST

Usando a API REST, escolha os pontos de extremidade específicos da região correta.
Consulte [APIs REST](rest-apis.md) para obter mais detalhes.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de fala](speech-sdk.md), as regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) no SDK de Fala para C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regiões para o reconhecimento de fala e tradução

A tabela abaixo lista as regiões disponíveis para **reconhecimento de fala** e **tradução**:

Região| Valor para o parâmetro de região no SDK de fala| Portal
-|-
Oeste dos EUA| `westus`| https://westus.cris.ai
Oeste dos EUA 2| `westus2`| https://westus2.cris.ai
Leste dos EUA| `eastus`| https://eastus.cris.ai
Leste dos EUA 2| `eastus2`| https://eastus2.cris.ai
Ásia Oriental| `eastasia`| https://eastasia.cris.ai
Sudeste da Ásia| `southeastasia`| https://southeastasia.cris.ai
Norte da Europa| `northeurope`| https://northeurope.cris.ai
Europa Ocidental|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Amostra para reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala estão listadas na [página da região do serviço](/azure/cognitive-services/luis/luis-reference-regions).
Para cada região de publicação listada, o parâmetro da região do SDK de Fala correspondente é determinado como a primeira parte do nome de domínio do ponto de extremidade.
Por exemplo, use `westus` para especificar a região de publicação Oeste dos EUA.
