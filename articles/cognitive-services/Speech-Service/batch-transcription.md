---
title: Use o Lote do Microsoft Azure de transcrição API
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
ms.openlocfilehash: 8a180dfada9da92e0b8ed69373a20602b3b0a177
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495588"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição do lote?

A transcrição em lote é ideal se você tiver grandes quantidades de áudio no armazenamento. Usando a API REST dedicado, você pode apontar para arquivos de áudio por uma assinatura de acesso compartilhado (SAS) URI e transcrições de recebimento de forma assíncrona.

## <a name="the-batch-transcription-api"></a>A API de transcrição em lote

A API de Transcrição em Lote oferece transcrição de fala para texto assíncrona, juntamente com recursos adicionais. É uma API REST que expõe métodos para:

1. Criando solicitações de processamento em lote
1. Status da consulta 
1. Fazer o download de transcrições

> [!NOTE]
> A API de Transcrição em Lote é ideal para call centers, que normalmente acumulam milhares de horas de áudio. A API é guiada por uma filosofia de "ignorar e esquecer", o que facilita a transcrição de grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição de lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| MP3 | PCM | 16-bit | 8 ou 16 kHz, mono, estéreo |
| OGG | OPUS | 16-bit | 8 ou 16 kHz, mono, estéreo |

> [!NOTE]
> A API de Transcrição em Lote requer uma chave S0 (camada pagadora). Ele não funciona com uma chave gratuita (f0).

Para fluxos de áudio estéreo, a API de transcrição de lotes divide os canais esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os timestamps por emissão permitem ao desenvolvedor criar uma transcrição final ordenada. A amostra JSON a seguir mostra a saída de um canal, incluindo as propriedades para configurar o filtro de profanação e o modelo de pontuação.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> A API de Transcrição em Lote usa um serviço REST para solicitar transcrições, seus status e resultados associados. É possível usar a API de qualquer linguagem. A próxima seção descreve como a API é usada.

### <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| Parâmetro | DESCRIÇÃO | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Especifica como lidar com conteúdo ofensivo nos resultados do reconhecimento. Os valores aceitos são `none`, o que desativa a filtragem de profanação, `masked` que substitui a profanidade por asteriscos, `removed` que remove todos os palavrões do resultado ou `tags`, que adiciona tags de "profanidade". A configuração padrão é `masked`. | Opcional |
| `PunctuationMode` | Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores aceitos são `none`, o que desativa a pontuação, `dictated` que implica pontuação explícita, `automatic` que permite ao decodificador lidar com pontuação ou `dictatedandautomatic`, o que implica em sinais de pontuação ditados ou automáticos. | Opcional |


## <a name="authorization-token"></a>Token de autorização

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](get-started.md). Se você planeja obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo que você precisa fazer. 

Se você planeja personalizar e usar um modelo personalizado, adicione a chave de assinatura ao portal de fala personalizado fazendo o seguinte:

1. Entre em [Fala Personalizada](https://customspeech.ai).

2. No canto superior direito, selecione **assinaturas**.

3. Selecione **conectar-se a assinatura existente**.

4. Na janela pop-up, adicione a chave de assinatura e um alias.

    ![A janela Adicionar assinatura](media/stt/Subscriptions.jpg)

5. Copie e cole essa chave no código do cliente no exemplo a seguir.

> [!NOTE]
> Se você planeja usar um modelo personalizado, também precisará da ID desse modelo. Este ID não é o ID do terminal que você encontra na visualização Detalhes do terminal. É a ID do modelo que você pode recuperar ao selecionar os detalhes desse modelo.

## <a name="sample-code"></a>Exemplo de código

Personalize o seguinte código de exemplo com uma chave de API e chave de assinatura. Essa ação permite que você obtenha um token de portador.

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

Depois de obter o token, especifique o URI do SAS que aponta para o arquivo de áudio que requer transcrição. O restante do código percorre o status e exibe os resultados. Primeiro, você configura a chave, a região, os modelos a serem usados e o SA, conforme mostrado no seguinte trecho de código. Em seguida, você instancia o cliente e a solicitação POST. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Agora que você fez a solicitação, é possível consultar e fazer o download dos resultados da transcrição, conforme mostrado no seguinte trecho de código:

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
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
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

Para detalhes completos sobre as chamadas anteriores, consulte o nosso [documento swagger](https://westus.cris.ai/swagger/ui/index). Para o exemplo completo mostrado aqui, vá para [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> No código anterior, a chave de assinatura é do recurso de fala que você cria no portal do Azure. As chaves obtidas do recurso do Serviço de Fala Personalizado não funcionam.

Anote a configuração assíncrona de áudio de lançamento e receber o status de transcrição. O cliente que você cria é um cliente HTTP .NET. Há um método `PostTranscriptions` para enviar os detalhes do arquivo de áudio e um método `GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou arquivos. Para especificar os modelos, você pode passar o mesmo método que os IDs de modelo para o modelo acústico e de idioma. 

Se você não quiser usar a linha de base, passe IDs de modelo para os modelos acústicos e de linguagem.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar os pontos finais dos modelos de linha de base. Se quiser usar modelos personalizados, forneça as IDs dos pontos de extremidade como o [Exemplo](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Se você quiser usar uma linha de base acústica com um modelo de idioma de linha de base, será necessário declarar apenas o ID de terminal do modelo personalizado. A Microsoft detecta o modelo de linha de base do parceiro, &mdash;seja acústico ou de linguagem &mdash;, e o utiliza para atender à solicitação de transcrição.

### <a name="supported-storage"></a>Armazenamento suportado

Atualmente, o único armazenamento suportado é o armazenamento do Azure Blob.

## <a name="download-the-sample"></a>Baixar o exemplo

Você pode encontrar a amostra neste artigo no [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Uma transcrição de áudio normalmente requer um intervalo de tempo igual à duração do arquivo de áudio, além de uma sobrecarga de dois a três minutos.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
