---
title: Amostra para Reconhecimento de Intenção | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Aqui está uma amostra para reconhecimento de intenção.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213190"
---
# <a name="sample-for-intent-recognition"></a>Amostra para reconhecimento de intenção

Primeiro, obtenha uma chave de assinatura. Em contraste com outros serviços suportados pelo Speech Service SDK do Cognitive Service, os serviços Intent Recognition requerem uma chave de assinatura específica. [Aqui](https://www.luis.ai) você pode encontrar informações adicionais sobre a tecnologia de reconhecimento de intenção, bem como informações sobre como adquirir uma chave de assinatura. Substitua a chave de assinatura de Reconhecimento Vocal, a [região da assinatura](regions.md) e o AppId do modelo de intenção nos locais apropriados nos exemplos.

## <a name="top-level-declarations"></a>Declarações de nível superior

Para todos os exemplos abaixo, as seguintes declarações de nível superior devem estar em vigor:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Reconhecimento de intenção usando microfone

O snippet de código abaixo mostra como reconhecer a intenção da entrada do microfone no idioma padrão (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Intenção de reconhecimento usando microfone em um idioma especificado

O trecho de código abaixo mostra como reconhecer a intenção da entrada de microfone em um idioma especificado, neste caso em alemão (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Intenção de reconhecimento de um arquivo, usando eventos

O trecho de código mostra como reconhecer a intenção no idioma padrão (`en-US`) de maneira contínua. Esse código permite acesso a informações adicionais, como resultados intermediários. A entrada é obtida de um arquivo de áudio, o formato com suporte é WAV/PCM de um canal (mono) com uma taxa de amostragem de 16 KHz.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximas etapas

- [Reconhecimento de fala](./speech-to-text-sample.md)

- [Tradução](./translation.md)
