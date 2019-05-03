---
title: Áudio de Stream compactado com o Speech SDK - serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado aos serviços de fala do Azure com o Speech SDK. Disponível para o C++, C#e o Java para Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: c6ab43b530c045eb4f2920b65f601ba981dfc8a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020760"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Áudio de Stream compactado com o SDK de fala

O SDK de fala **Stream de entrada de áudio compactados** API fornece uma maneira de transmitir áudio compactado para o serviço de fala usando PullStream ou PushStream.

> [!IMPORTANT]
> Fluxo de áudio compactado somente há suporte para C++, C#e o Java no Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9).
> O suporte é limitado a MP3 e OPUS/OGG.

## <a name="prerequisites"></a>Pré-requisitos

Instale essas dependências adicionais para usar a entrada de áudio compactada com o SDK de fala para Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Fluxo de áudio compactado

Para transmitir em um formato de áudio compactado para os serviços de fala, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie um `AudioConfig` de uma instância de sua classe de fluxos, especificando o formato de compactação do fluxo.

Vamos supor que você tenha uma classe de fluxo de entrada chamada `myPushStream` e estiver usando OPUS/OGG. Seu código pode ter esta aparência:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
