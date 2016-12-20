---
title: Codificar um ativo com o Media Encoder Standard usando o .NET | Microsoft Docs
description: "Este tópico mostra como usar o .NET para codificar um ativo com o Codificador de Mídia Padrão."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6031391dddc9f5c283974bd22ad22d60d0697a4d


---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com o Codificador de Mídia Padrão usando o .NET
Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. 

Este tópico mostra como usar o .NET para codificar seus ativos com o MES (Codificador de Mídia Padrão). O Codificador de Mídia Padrão é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> O MES produz um arquivo de saída com um nome que contém os primeiros 32 caracteres do nome do arquivo de entrada. O nome é baseado no que é especificado no arquivo de predefinição. Por exemplo, "FileName": "{Basename}_{Index}{Extension}". {Basename} é substituído pelos primeiros 32 caracteres do nome do arquivo de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos de MES
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições de MES
O Codificador de Mídia Padrão é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Ao codificar um ativo (ou ativos) de entrada usando MES, você obtém um ativo de saída na conclusão bem-sucedida da tarefa de codificação. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc., com base na predefinição de codificação utilizada.

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: <asset_id>_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), em que <asset_id> é o valor de AssetId do ativo de entrada. O esquema desse XML de metadados de entrada é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: <nome_arquivo_origem>_manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema desse XML de metadados de saída é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se desejar examinar qualquer um dos dois arquivos de metadados, você poderá criar um localizador SAS e baixe o arquivo em seu computador local. É possível encontrar um exemplo de como criar um localizador SAS e baixar um arquivo usando as Extensões do SDK do .NET para os Serviços de Mídia.

## <a name="download-sample"></a>Baixar exemplo
Obtenha e execute um exemplo [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="example"></a>Exemplo
O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

* Crie um trabalho de codificação.
* Obtenha uma referência para o Codificador de Mídia Padrão.
* Especifique o uso da predefinição "720p com várias taxas de bits H264". Você pode ver todas as predefinições [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Você também pode examinar [aqui](https://msdn.microsoft.com/library/mt269962.aspx) neste tópico o esquema que essas predefinições devem seguir.
* Adicione uma única tarefa de codificação para o trabalho. 
* Especifique o ativo de entrada a ser codificado.
* Crie um ativo de saída que conterá o ativo codificado.
* Adicione um manipulador de eventos para verificar o progresso do trabalho.
* Enviar o trabalho.
  
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

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


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Como gerar miniatura usando o Media Encoder Standard com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Visão geral de codificação dos Serviços de Mídia](media-services-encode-asset.md)




<!--HONumber=Nov16_HO3-->


