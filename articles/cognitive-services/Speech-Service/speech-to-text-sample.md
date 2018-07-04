---
title: Amostra para Fala para Texto | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Aqui está uma amostra para fala para texto.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030243"
---
# <a name="sample-for-speech-to-text"></a>Amostra para Speech-to-Text

> [!NOTE]
> Para obter instruções para fazer o download desta amostra e de outras, consulte [Samples for Speech SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Para todos os exemplos abaixo, as seguintes declarações de nível superior devem estar em vigor:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Reconhecimento de fala usando o microfone

O trecho de código abaixo mostra como reconhecer a entrada de fala do microfone no idioma padrão (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Reconhecimento de fala de um arquivo

O trecho de código a seguir reconhece a entrada de fala de um arquivo de áudio no idioma padrão (`en-US`), o formato suportado é WAV / PCM de um único canal (mono) com uma taxa de amostragem de 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Reconhecimento de fala usando um modelo personalizado

O [Custom Speech Service (CRIS)](https://www.cris.ai/) permite a personalização do mecanismo de fala para texto da Microsoft para seu aplicativo. O trecho abaixo mostra como reconhecer a fala de um microfone usando seu modelo CRIS; preencha sua chave de assinatura CRIS e sua própria identificação de implantação antes de executá-la.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Reconhecimento de fala contínua

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exemplo de código fonte

A versão mais recente dos exemplos e amostras ainda mais avançadas estão em um repositório [dedicado do GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Próximas etapas

- [Reconhecimento de intenção](./intent.md)

- [Tradução](./translation.md)
