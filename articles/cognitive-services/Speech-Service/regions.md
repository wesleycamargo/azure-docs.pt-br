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
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324384"
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

Região| Valor para o parâmetro de região no SDK de fala
-|-
Oeste dos EUA| `westus`
Ásia Oriental| `eastasia`
Norte da Europa| `northeurope`

### <a name="regions-for-intent-recognition"></a>Amostra para reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala estão listadas na [página da região do serviço](/azure/cognitive-services/luis/luis-reference-regions).
Para cada região de publicação listada, o parâmetro da região do SDK de Fala correspondente é determinado como a primeira parte do nome de domínio do ponto de extremidade.
Por exemplo, use `westus` para especificar a região de publicação Oeste dos EUA.
