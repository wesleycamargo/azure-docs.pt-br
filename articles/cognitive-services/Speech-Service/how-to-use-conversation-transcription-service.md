---
title: Transcrição de vários participantes conversas com o Speech SDK - serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa com o SDK de fala. Disponível para C++, C#e Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jhakulin
ms.openlocfilehash: e9de4faf18c54f7c7582ef5a8ab0648629d4f48e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190145"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcrição de vários participantes conversas com o SDK de fala

O SDK de fala **ConversationTranscriber** API permite que você transcrever reuniões/conversas com a capacidade de adicionar, remover e identificar os participantes por fluxo de áudio para o serviços de fala usando `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limitações

* Transcriber conversa tem suporte para C++, C#e o Java no Android, Linux e Windows.
* O Kit de desenvolvimento ROOBO é o ambiente de hardware com suporte para a criação conversas que fornece a matriz de microfone várias circular que pode ser utilizado com eficiência pelo serviço de transcrição de conversa para a identificação do locutor. [Para obter mais informações, consulte o SDK de dispositivos de fala](speech-devices-sdk.md).
* Suporte do SDK de fala é limitado a usar áudio pull e push fluxos de modo com oito canais de áudio PCM.

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba como usar a fala em texto com o SDK de fala.](quickstart-csharp-dotnet-windows.md)
* [Obtenha sua assinatura de avaliação de fala.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Criar assinaturas de voz para os participantes

A primeira etapa é criar assinaturas de voz para os participantes da conversa. Criando assinaturas de voz é necessário para a identificação do locutor eficiente.
No exemplo a seguir, vamos [usar a API REST para obter a assinatura de voz.](https://aka.ms/cts/signaturegenservice)

O exemplo a seguir mostra duas maneiras diferentes de criar assinaturas de voz:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Transcrever conversas

Para transcrever conversas com vários participantes, criar o `ConversationTranscriber` objeto que está associado com o `AudioConfig` objeto criado para a sessão de conversa e o fluxo de áudio usando `PullAudioInputStream` ou `PushAudioInputStream`.

Vamos supor que você tenha uma classe ConversationTranscriber chamada `MyConversationTranscriber`. Seu código pode ter esta aparência:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
