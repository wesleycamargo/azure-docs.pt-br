---
title: Conceitos de fluxo de entrada de áudio do SDK de Fala
description: Uma visão geral dos recursos da API de fluxo de entrada de áudio do SDK de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 6c2d7c5787305f60b73ab83ea17367b04e03ac12
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985173"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre a API de fluxo de entrada de áudio do SDK de Fala

A API do **Fluxo de Entrada de Áudio** do SDK de Fala fornece uma maneira de transmitir fluxos de áudio nos reconhecedores, em vez de usar o microfone ou as APIs de arquivo de entrada.

As etapas a seguir são necessárias ao usar fluxos de entrada de áudio:

- Identificar o formato do fluxo de áudio. O formato deve ter suporte no SDK de Fala e no serviço de Fala. Atualmente, apenas a configuração a seguir tem suporte:

  Exemplos de áudio no formato PCM, um canal, 16 mil amostras por segundo, 32 mil bytes por segundo, dois alinhamentos de bloco (16 bits, incluindo preenchimento para uma amostra), 16 bits por amostra.

  O código correspondente no SDK para criar o formato de áudio tem esta aparência:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Garanta que seu código possa fornecer os dados de áudio RAW de acordo com estas especificações. Se os dados de origem do áudio não corresponderem aos formatos com suporte, o áudio deverá ser transcodificado no formato exigido.

- Crie sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback`. Implemente os membros `Read()` e `Close()`. A assinatura de função exata é dependente de idioma, mas o código será semelhante a este exemplo de código:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public size_t Read(byte *buffer, size_t size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Crie uma configuração de áudio com base em seu formato e fluxo de entrada de áudio. Passe sua configuração de fala normal e a configuração de entrada de áudio ao criar seu reconhecedor. Por exemplo: 

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
