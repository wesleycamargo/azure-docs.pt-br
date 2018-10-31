---
title: API de transcrição de Lote do Azure
titlesuffix: Azure Cognitive Services
description: Exemplos para transcrevê grandes volumes de conteúdo de áudio.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: e7523bf97d6252422ebb853b818453c935640f50
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648795"
---
# <a name="batch-transcription"></a>Transcrição de lote

A transcrição em lote é ideal se você tiver grandes quantidades de áudio no armazenamento. Usando nossa API Rest, você pode apontar para arquivos de áudio por URI SAS e receber assinaturas de forma assíncrona.

## <a name="batch-transcription-api"></a>Transcrição de lote API

A API de transcrição de Lote oferece transcrição de conversão de fala em texto em conjunto com recursos adicionais. É um método de exposição da API REST para:

1. Criando solicitações de processamento em lote

2. Status da consulta 

3. Baixando transcrições

> [!NOTE]
> A API de transcrição de Lote é ideal para call centers, que normalmente acumulam milhares de horas de áudio. A API é guiada por uma filosofia "disparar e esquecer", o que facilita a transcrição de grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de Lote dá suporte aos seguintes formatos:

NOME| Canal  |
----|----------|
mp3 |   Mono   |   
mp3 |  Estéreo  | 
WAV |   Mono   |
WAV |  Estéreo  |

Para transmissões de áudio estéreo, a transcrição de Lote divide os canais esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os timestamps por emissão permitem ao desenvolvedor criar uma transcrição final ordenada. A amostra JSON a seguir mostra a saída de um canal.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> A API de transcrição em lote está usando um serviço REST para solicitar transcrições, seus status e resultados associados. É possível usar a API de qualquer linguagem. A próxima seção descreve como ela é usada.

## <a name="authorization-token"></a>Token de autorização

Assim como todos os recursos do Serviço de Fala, você cria uma chave de assinatura no [portal do Azure](https://portal.azure.com) seguindo nosso [Guia de introdução](get-started.md). Se você planeja obter transcrições de nossos modelos de linha de base, isso é tudo o que você precisa fazer. 

Se você pretende personalizar e usar um modelo personalizado, precisará adicionar essa chave de assinatura ao portal de fala personalizada da seguinte forma:

1. Entre em [Fala Personalizada](https://customspeech.ai).

2. Selecione **Assinaturas**.

3. Selecione **Conectar Assinatura Existente**.

4. Adicionar a chave de assinatura e um alias na exibição que aparece

    ![Captura de tela da página Assinaturas de Fala Personalizada](media/stt/Subscriptions.jpg)

5. Copie e cole essa chave no código do cliente no exemplo a seguir.

> [!NOTE]
> Se você planeja usar um modelo personalizado, também precisará da ID desse modelo. Observe que essa não é a ID do ponto de extremidade que você encontra no modo de exibição Detalhes do Ponto de Extremidade. É a ID do modelo que você pode recuperar ao selecionar os detalhes desse modelo.

## <a name="sample-code"></a>Exemplo de código

Personalize o seguinte código de exemplo com uma chave de API e chave de assinatura. Isso permite que você obtenha um token de portador.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

Após obter o token, será necessário especificar o URI do SAS apontando para o arquivo de áudio que requer a transcrição. O restante do código percorre o status e exibe os resultados. Inicialmente, seria necessário configurar a chave, a região, os modelos a serem usados e a SA. conforme mostrado no snippet de código abaixo. Isso é seguido pela instanciação do cliente e da solicitação POST. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "some SAS URI";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("some guid");
            private static Guid AdaptedLanguageId = new Guid("some guid");

            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Agora que a solicitação foi feita, o usuário pode consultar e baixar os resultados da transcrição, como mostra o snippet de código.

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not creted form here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successfull, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succedded. Results: ");
                            Console.WriteLine(results);
                        }
                    
                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;
                    
                    }
                }
            }
        }
```

Nosso [documento Swagger](https://westus.cris.ai/swagger/ui/index) fornece detalhes completos sobre as chamadas acima. O exemplo completo mostrado aqui é na [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> No código anterior, a chave de assinatura é do recurso de Fala que você cria no portal do Azure. Chaves obtidas do recurso de Serviço de Fala Personalizado não funcionam.

Observe a configuração assíncrona para postar o status de transcrição de áudio e recebimento. O cliente criado é um cliente Http .NET. Existe um método `PostTranscriptions`para enviar os detalhes do arquivo de áudio e um método`GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` usa esse identificador para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica nenhum modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou arquivos. Para especificar os modelos, é possível passar o mesmo método para as IDs do modelo de linguagem e acústico. 

Se você não quiser usar a linha de base, deverá passar as IDs do modelo para ambos os modelos de linguagem e acústicos.

> [!NOTE]
> Para a transcrição de linha de base, não é necessário declarar os pontos de extremidade dos modelos de linha de base. Se quiser usar modelos personalizados, forneça as IDs dos pontos de extremidade como o [Exemplo](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Se quiser usar uma linha de base acústica com um modelo de linguagem de linha de base, será necessário somente declarar a ID do ponto de extremidade do modelo personalizado. A Microsoft detecta o modelo de linha de base do parceiro (seja ele acústico ou de linguagem) e usa isso para atender à solicitação de transcrição.

### <a name="supported-storage"></a>Armazenamento suportado

Atualmente, o único armazenamento com suporte é o Armazenamento de Blobs do Azure.

## <a name="downloading-the-sample"></a>Baixar o exemplo

O exemplo mostrado aqui está no [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Normalmente, uma transcrição de áudio requer um intervalo de tempo igual à duração do arquivo de áudio, além de uma sobrecarga de 2 a 3 minutos.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
