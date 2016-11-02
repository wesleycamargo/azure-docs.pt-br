<properties
    pageTitle="Indexando os Arquivos de Mídia com a Visualização do Indexador de Mídia do Azure 2 | Microsoft Azure"
    description="O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Este tópico mostra como usar a Visualização do Indexador de Mídia 2 ."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>



# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexando arquivos de mídia com a Preview do Indexador de Mídia do Azure 2

##<a name="overview"></a>Visão geral

O MP (processador de mídia) da **Preview do Indexador de Mídia do Azure 2** permite tornar conteúdo e arquivos de mídia pesquisáveis, bem como gerar faixas de legendagem oculta. Comparada à versão anterior do [Indexador de Mídia do Azure](media-services-index-content.md), a **Preview do Indexador de Mídia do Azure 2** executa uma indexação mais rápida e oferece maior suporte a idiomas. Dentre os idiomas com suporte estão o inglês, espanhol, francês, alemão, italiano, chinês, português e árabe.

No momento, o MP da **Preview do Indexador de Mídia do Azure 2** está em Preview.

Este tópico mostra como criar trabalhos de indexação com a **Visualização do Indexador de Mídia do Azure 2**.

>[AZURE.NOTE]As seguintes considerações se aplicam:
>
>Não há suporte para o Indexador 2 no Azure China e no Azure Governamental.
>
>A preview é limitada a cerca de 10 minutos de processamento, mas é gratuita para todos os clientes.
>
>Quanto a indexação de conteúdo, certifique-se de usar os arquivos de mídia com fala muito clara (sem música em segundo plano, ruído, efeitos ou assovio no microfone). Alguns exemplos de conteúdo apropriado são: reuniões, palestras e apresentações registradas. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, tudo com áudio misto e efeitos de som, com conteúdo mal gravado com ruídos de fundo (assovio).


Este tópico fornece detalhes sobre a **Visualização do Azure Media Indexer 2** e mostra como usá-la com o SDK dos Serviços de Mídia para .NET

##<a name="input-and-output-files"></a>Arquivos de entrada e saída

###<a name="input-files"></a>Arquivos de entrada

Arquivos de áudio ou vídeo

###<a name="output-files"></a>Arquivos de saída

Um trabalho de indexação pode gerar arquivos de legenda oculta nos seguintes formatos:  

- **SAMI**
- **TTML**
- **WebVTT**

Arquivos de CC (Legenda Oculta) nesses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis para pessoas com deficiência auditiva.

##<a name="task-configuration-(preset)"></a>Configuração de tarefa (predefinição)

Ao criar uma tarefa de indexação com a **Preview do Indexador de Mídia do Azure 2**, é necessário especificar uma predefinição de configuração.

O JSON a seguir define os parâmetros disponíveis.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Idiomas com suporte  

A Preview do Indexador de Mídia do Azure 2 dá suporte à conversão de fala em texto para os seguintes idiomas (ao especificar o nome do idioma na configuração de tarefa, use o código de 4 caracteres entre colchetes, como mostrado abaixo):

- Inglês [EnUs]
- Espanhol [EsEs]
- Chinês [ZhCn]
- Francês [FrFr]
- Alemão [DeDe]
- Italiano [ItIt]
- Português [PtBr]
- Árabe (Egípcio) [ArEg]


## <a name="sample-code"></a>Exemplo de código

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
1. Criar um trabalho com uma tarefa de indexação baseada em um arquivo de configuração que contém a predefinição de JSON a seguir.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Baixar os arquivos de saída. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
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
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        
                    progressJobTask.Wait();
        
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
                }
        
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
        
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
        
                    return asset;
                }
        
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
        
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
        
                    return processor;
                }
        
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
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
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }


##<a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Links relacionados

[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


<!--HONumber=Oct16_HO2-->


