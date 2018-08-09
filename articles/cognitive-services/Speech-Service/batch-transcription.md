---
title: Transcrição de lote API do Lote do Microsoft Azure
description: Exemplos
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: f21973855ceb3a257627c147490ac50465c54020
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281932"
---
# <a name="batch-transcription"></a>Transcrição de lote

Transcrição de lote é ideal para casos de uso com grandes quantidades de áudio. Ele permite que o desenvolvedor apontam para arquivos de áudio e voltar transcrições no modo assíncrono.

## <a name="batch-transcription-api"></a>Transcrição de lote API

A transcrição do lote API possibilita o cenário acima. Oferece fala assíncrona para transcrição de texto, juntamente com recursos adicionais.

> [!NOTE]
> A transcrição do lote API é ideal para Call Centers que normalmente se acumulam milhares de horas de áudio. A filosofia Fire & Forget da API facilita a transcrição de grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição em lote tem como objetivo tornar-se de fato para todos os cenários relacionados ao call center off-line e oferecer suporte para todos os formatos relacionados. Formatos atualmente suportados:

NOME| Canal  |
----|----------|
mp3 |   Mono   |   
mp3 |  Estéreo  | 
WAV |   Mono   |
WAV |  Estéreo  |

Para transmissões de áudio estéreo, a transcrição em lote dividirá os canais esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os timestamps por emissão permitem ao desenvolvedor criar uma transcrição final ordenada. A amostra JSON a seguir mostra a saída de um canal.

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
> A API de transcrição em lote está usando um serviço REST para solicitar transcrições, seus status e resultados associados. A API pode ser usada em qualquer idioma. A próxima seção descreve como ela é usada.

## <a name="authorization-token"></a>Token de autorização

Assim como todos os recursos do Unified Speech Service, o usuário precisa criar uma chave de assinatura no [Portal do Azure](https://portal.azure.com). Além disso, uma chave de API precisa ser adquirida no portal de fala. As etapas para gerar uma chave de API:

1. Faça o login em https://customspeech.ai.

2. Clique em Assinaturas.

3. Clique na opção `Generate API Key`.

    ![A exibição de upload](media/stt/Subscriptions.jpg)

4. Copie e cole essa chave no código do cliente no exemplo abaixo.

> [!NOTE]
> Se você planeja usar um modelo personalizado, também precisará do ID desse modelo. Observe que essa não é a ID de implantação ou de terminal que você encontra na visualização Detalhes do terminal, mas o ID do modelo que pode recuperar ao clicar nos Detalhes desse modelo

## <a name="sample-code"></a>Exemplo de código

Fazendo uso da API é bastante simples. É necessário que o código de exemplo abaixo seja personalizado com uma chave de assinatura e uma chave de API que, por sua vez, permitirão que o desenvolvedor obtenha um token de portador, como mostra o trecho de código a seguir:

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

Quando o token for obtido, o desenvolvedor deverá especificar Uri de SAS apontando para o arquivo de áudio que requer a transcrição. O restante do código simplesmente percorre o status e exibe os resultados.

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
> A chave de assinatura mencionada no snippet de código acima é a chave do recurso Speech (Preview) que você cria no portal do Azure. As chaves obtidas do recurso Custom Speech Service não funcionarão.


Observe a configuração assíncrona para postar o status de transcrição de áudio e recebimento. O cliente criado é um cliente Http .NET. Existe um método `PostTranscriptions`para enviar os detalhes do arquivo de áudio e um método`GetTranscriptions` para receber os resultados. `PostTranscriptions`retorna um identificador e o método`GetTranscriptions` está usando esse identificador para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica nenhum modelo personalizado. O serviço usará os modelos de linha de base para transcrever o (s) arquivo (s). Se o usuário desejar especificar os modelos, pode-se passar o mesmo método para os modelIDs para o modelo acústico e de linguagem. 

Se não desejar usar a linha de base, é preciso passar os IDs do modelo para os modelos acústicos e de linguagem.

> [!NOTE]
> Para transcrição de linha de base, o usuário não precisa declarar os pontos finais dos modelos de linha de base. Se o usuário quiser usar modelos personalizados, ele deverá fornecer seus IDs de pontos de extremidade como [Amostra](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Se o usuário quiser usar uma linha de base acústica com um modelo de idioma de linha de base, ele só terá que declarar o ID de terminal do modelo personalizado. Internamente, nosso sistema descobrirá o modelo de linha de base do parceiro (seja ele acústico ou de linguagem) e o usará para preencher a solicitação de transcrição.

### <a name="supported-storage"></a>Armazenamento suportado

Atualmente, o único armazenamento suportado é o blob do Azure.

## <a name="downloading-the-sample"></a>Download da amostra

O exemplo exibido aqui está no [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Normalmente, uma transcrição de áudio requer um intervalo de tempo igual à duração do arquivo de áudio mais uma sobrecarga de 2 a 3 minutos.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
