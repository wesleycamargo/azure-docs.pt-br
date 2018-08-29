---
title: API de transcrição de Lote do Azure
description: Exemplos
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 5af829ca076b39758973c28a44d918b9ba5782b1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42351243"
---
# <a name="batch-transcription"></a>Transcrição de lote

A transcrição de Lote será ideal, se você tiver grandes quantidades de áudio. Você pode apontar para arquivos de áudio e recuperar transcrições no modo assíncrono.

## <a name="batch-transcription-api"></a>Transcrição de lote API

A API de transcrição de Lote oferece transcrição de conversão de fala em texto em conjunto com recursos adicionais.

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

Como acontece com todos os recursos do Serviço de Fala Unificado, você cria uma chave de assinatura no [portal do Azure](https://portal.azure.com). Além disso, você adquire uma chave de API no portal do Speech: 

1. Entre em [Fala Personalizada](https://customspeech.ai).

2. Selecione **Assinaturas**.

3. Selecione **Gerar chave de API**.

    ![Captura de tela da página Assinaturas de Fala Personalizada](media/stt/Subscriptions.jpg)

4. Copie e cole essa chave no código do cliente no exemplo a seguir.

> [!NOTE]
> Se você planeja usar um modelo personalizado, também precisará da ID desse modelo. Observe que essa não é a ID do ponto de extremidade nem de implantação que você encontra no modo de exibição Detalhes do Ponto de Extremidade. É a ID do modelo que você pode recuperar ao selecionar os detalhes desse modelo.

## <a name="sample-code"></a>Exemplo de código

Personalize o seguinte código de exemplo com uma chave de API e chave de assinatura. Isso permite que você obtenha um token de portador.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

Após obter o token, será necessário especificar o URI do SAS apontando para o arquivo de áudio que requer a transcrição. O restante do código percorre o status e exibe os resultados.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> No código anterior, a chave de assinatura é do recurso de Fala (versão prévia) que você cria no portal do Azure. Chaves obtidas do recurso de Serviço de Fala Personalizado não funcionam.

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
