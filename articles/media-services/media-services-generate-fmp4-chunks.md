---
title: "Criar uma tarefa de codificação dos Serviços de Mídia do Azure que gere partes fMP4 | Microsoft Docs"
description: "Este tópico mostra como criar uma tarefa de codificação que gere partes fMP4. Quando essa tarefa for usada com o Media Encoder Standard ou o codificador Fluxo de Trabalho Premium de Codificação de Mídia, o ativo de saída conterá partes fMP4 em vez de arquivos ISO MP4."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: b7029ac5-eadd-4a2f-8111-1fc460828981
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2970606fb9a88b23d958b9e20a981e2ecbe72943
ms.openlocfilehash: 9b373d3174592d26b74896eda712ff7cae1f787e
ms.lasthandoff: 02/28/2017


---
#  <a name="create-an-encoding-task-that-generates-fmp4-chunks"></a>Criar uma tarefa de codificação que gere parte fMP4

## <a name="overview"></a>Visão geral

Este tópico mostra como criar uma tarefa de codificação que gere partes fMP4 (MP4 fragmentado) em vez de arquivos ISO MP4. Para gerar partes fMP4, use o **Media Encoder Standard** ou o codificador **Fluxo de Trabalho Premium de Codificação de Mídia** para criar uma tarefa de codificação e também especificar a opção **AssetFormatOption.AdaptiveStreaming**, conforme mostrado neste trecho de código:  
    
    task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks", 
            options: AssetCreationOptions.None, 
            formatOption: AssetFormatOption.AdaptiveStreaming);


## <a id="encoding_with_dotnet"></a>Codificação com o SDK do .NET dos Serviços de Mídia

O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o codificador **Media Encoder Standard**.
- Adicione uma tarefa de codificação ao trabalho e especifique para usar o **Streaming Adaptável** predefinido. 
- Crie um ativo de saída que conterá partes fMP4 e um arquivo .ism.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Enviar o trabalho.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreaming
        {
            class Program
            {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                        _mediaServicesAccountName,
                        _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Get an uploaded asset.
                var asset = _context.Assets.FirstOrDefault();

                // Encode and generate the output using the "Adaptive Streaming" preset.
                EncodeToAdaptiveBitrateMP4Set(asset);

                Console.ReadLine();
            }
            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Media Encoder Standard Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 

                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                // It is also specified to use AssetFormatOption.AdaptiveStreaming, 
                // which means the output asset will contain fMP4 chunks.

                task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks",
                    options: AssetCreationOptions.None,
                    formatOption: AssetFormatOption.AdaptiveStreaming);

                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                job.Submit();
                job.GetExecutionProgressTask(CancellationToken.None).Wait();

                return job.OutputMediaAssets[0];
            }
            private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
                switch (e.CurrentState)
                {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
                }
            }
            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

            }

        }

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Visão geral da codificação de serviços de mídia](media-services-encode-asset.md)


