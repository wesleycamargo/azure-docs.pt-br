---
title: Conceitos de AudioInputStream | Microsoft Docs
description: Uma visão geral dos recursos da API de AudioInputStream.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35364958"
---
# <a name="about-the-audio-input-stream-api"></a>Sobre a API de fluxo de entrada de áudio

A API do **Fluxo de Entrada de Áudio** fornece uma maneira de transmitir fluxos de áudio nos reconhecedores em vez de usar o microfone ou as APIs de arquivo de entrada.

## <a name="api-overview"></a>Visão geral da API

A API usa dois componentes, o `AudioInputStream` (os dados de áudio brutos) e o `AudioInputStreamFormat`.

O `AudioInputStreamFormat` define o formato dos dados de áudio. Ele pode ser comparado com a estrutura `WAVEFORMAT` padrão de arquivos wave no Windows.

  - `FormatTag`

    O formato do áudio. Atualmente, o SDK de Fala dá suporte apenas a `format 1` (PCM – little-endian).

  - `Channels`

    O número de canais. O serviço de fala atual oferece suporte apenas a materiais com um canal de áudio (mono).

  - `SamplesPerSec`

    A taxa de amostragem. Uma gravação típica com microfone tem 16000 amostras por segundo.

  - `AvgBytesPerSec`

    Média de bytes por segundo, calculada como `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. A média de bytes por segundo pode ser diferente para fluxos de áudio que usam taxas de bits variáveis.

  - `BlockAlign`

    O tamanho de um único quadro, calculado como `Channels * ceil(wBitsPerSample, 8)`. Devido ao preenchimento, o valor real pode ser maior do que esse valor.

  - `BitsPerSample`

    Os bits por amostra. Um fluxo de áudio típico usa 16 bits por amostra (qualidade de CD).

A classe base `AudioInputStream` será substituída por seu adaptador de fluxo personalizado. Esse adaptador tem que implementar estas funções:

   - `GetFormat()`

     Essa função é chamada para obter o formato do fluxo de áudio. Ela obtém um ponteiro para o buffer AudioInputStreamFormat.

   - `Read()`

     Essa função é chamada para obter dados do fluxo de áudio. Um parâmetro é um ponteiro para o buffer no qual copiar os dados de áudio. O segundo parâmetro é o tamanho do buffer. A função retorna o número de bytes copiados para o buffer. Um valor de retorno de `0` indica o fim do fluxo.

   - `Close()`

     Essa função é chamada para fechar o fluxo de áudio.

## <a name="usage-examples"></a>Exemplos de uso

Em geral, as etapas a seguir são incluídas ao usar fluxos de entrada de áudio:

  - Identificar o formato do fluxo de áudio. O formato deve ter suporte do SDK e do serviço de fala. Atualmente, a configuração a seguir tem suporte:

    Uma marca de formato de áudio (PCM), um canal, 16.000 amostras por segundo, 32.000 bytes por segundo, dois alinhamentos de bloco (16 bits, incluindo preenchimento para uma amostra), 16 bits por amostra

  - Verifique se o seu código pode fornecer os dados de áudio BRUTOS com relação às especificações identificadas acima. Se os dados de origem do áudio não corresponderem aos formatos com suporte, o áudio deverá ser transcodificado no formato exigido.

  - Derive sua classe de fluxo de entrada de áudio personalizada de `AudioInputStream`. Implemente a operação `GetFormat()`, `Read()` e `Close()`. A assinatura de função exata depende da linguagem, mas o código será semelhante a este exemplo de código:

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
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

  - Use seu fluxo de áudio de entrada:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - Em algumas linguagens, o `contosoStream` deve ser excluído explicitamente após a conclusão do reconhecimento. Você não pode liberar o AudioStream antes da leitura da entrada completa. Em um cenário que usa `StopContinuousRecognitionAsync` e `StopContinuousRecognitionAsync`, é necessário ter um conceito ilustrado neste exemplo:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-windows.md)
