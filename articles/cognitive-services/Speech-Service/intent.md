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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045003"
---
# <a name="sample-for-intent-recognition"></a>Amostra para reconhecimento de intenção

> [!NOTE]
> Para obter instruções para fazer o download desta amostra e de outras, consulte [Samples for Speech SDK](samples.md).

> [!NOTE]
> Por favor, obtenha uma chave de assinatura primeiro. Em contraste com outros serviços suportados pelo Speech Service SDK do Cognitive Service, os serviços Intent Recognition requerem uma chave de assinatura específica. [Aqui](https://www.luis.ai) você pode encontrar informações adicionais sobre a tecnologia de reconhecimento de intenção, bem como informações sobre como adquirir uma chave de assinatura. Substitua sua própria chave de assinatura, a região do serviço e o AppId de seu modelo de intenção no local apropriado nas amostras.

> [!NOTE]
> Para todos os exemplos abaixo, as seguintes declarações de nível superior devem estar em vigor:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Reconhecimento de intenção usando microfone

O snippet de código abaixo mostra como reconhecer a intenção da entrada do microfone no idioma padrão (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Intenção de reconhecimento usando microfone em um idioma especificado

O trecho de código abaixo mostra como reconhecer a intenção da entrada de microfone em um idioma especificado, neste caso em alemão (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Intenção de reconhecimento de um arquivo

O trecho de código a seguir reconhece a intenção de um arquivo de áudio no idioma padrão (`en-US`), o formato suportado é WAV / PCM de um único canal (mono) com uma taxa de amostragem de 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Intenção de reconhecimento usando eventos

O trecho de código mostra como reconhecer a intenção de maneira contínua. Esse código permite acesso a informações adicionais, como resultados intermediários. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Exemplo de código fonte

Para obter o conjunto de amostras mais recente, consulte o [repositório GitHub de Amostra do SDK do Speech Services Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Próximas etapas

- [Reconhecimento de fala](./speech-to-text-sample.md)

- [Tradução](./translation.md)
