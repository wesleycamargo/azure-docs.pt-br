<properties
	pageTitle="Usar as Miniaturas de Vídeo de Mídia do Azure para criar um resumo de vídeo"
	description="O resumo de vídeo pode ajudá-lo a criar resumos de vídeos de longa duração com a seleção automática de trechos interessantes do vídeo de origem. Isso será útil quando você desejar fornecer uma visão geral rápida do que esperar de um vídeo de longa duração."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/14/2016"   
	ms.author="milanga;juliako;"/>

#Usar as Miniaturas de Vídeo de Mídia do Azure para criar um resumo de vídeo
##Visão geral

O MP (processador de mídia) das **Miniaturas de Vídeo de Mídia do Azure** permite criar o resumo de um vídeo que é grande utilidade para clientes que desejam apenas visualizar um resumo de um vídeo de longa duração. Por exemplo, os clientes talvez queiram ver um breve “vídeo resumido” ao focalizar uma miniatura. Ao ajustar os parâmetros das **Miniaturas de Vídeo de Mídia do Azure** por meio de uma predefinição de configuração, é possível usar a avançada tecnologia de detecção de captura e concatenação do MP para gerar de forma algorítmica um subclipe descritivo.

No momento, o MP da **Miniatura de Vídeo de Mídia do Azure** está em Preview.

Este tópico fornece detalhes sobre a **Miniatura de Vídeo de Mídia do Azure** e mostra como usá-las com o SDK dos Serviços de Mídia para .NET

##Exemplo de resumo de vídeo 

Apresentamos abaixo alguns exemplos do que o processador de mídia das Miniaturas de Vídeo de Mídia do Azure é capaz de fazer:

###Vídeo original

[Vídeo original](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###Resultado da miniatura de vídeo

[Resultado da miniatura de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Configuração de tarefa (predefinida)

Ao criar uma tarefa de miniatura de vídeo com as **Miniaturas de Vídeo de Mídia do Azure**, é necessário especificar uma predefinição de configuração. O exemplo de miniatura acima foi criado com a seguinte configuração básica do JSON:

	{"version":"1.0"}

No momento, é possível alterar os seguintes parâmetros:

Param|Descrição
---|---
outputAudio|Especifica se o vídeo resultante conterá áudio. <br/>Valores permitidos: True ou False. Padrão: True.
fadeInFadeOut|Especifica se as transições de esmaecimento serão usadas entre as miniaturas de movimento separadas. <br/>Valores permitidos: True ou False. Padrão: True.
maxMotionThumbnailDurationInSecs|Inteiro que especifica qual será a duração de todo o vídeo resultante. O padrão depende da duração do vídeo original.


A tabela a seguir descreve a duração padrão, quando **maxMotionThumbnailInSecs** não é usado.

||||
---|---|---|---|---
Duração do vídeo|d < 3 min|3 min < d < 15 min|15 min < d < 30 min| 30 min < d
Duração da miniatura|15 s (2 a 3 cenas)| 30 s (3 a 5 cenas)|60 s (5 a 10 cenas)|90 s (10 a 15 cenas)


O JSON a seguir define os parâmetros disponíveis.
	
	{
	    "version": "1.0",
	    "options": {
	        "outputAudio": "true",
	        "maxMotionThumbnailDurationInSecs": "10",
	        "fadeInFadeOut": "true"
	    }
	}

## Exemplo de código

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
1. Criar um trabalho com uma miniatura de vídeo baseada em um arquivo de configuração que contém a predefinição de JSON a seguir. 
		
		{				
			"version": "1.0",
		    "options": {
		        "outputAudio": "true",
	    	    "maxMotionThumbnailDurationInSecs": "30",
	    	    "fadeInFadeOut": "false"
		    }
		}

1. Baixar os arquivos de saída.

###Código do .NET
	 
    using System;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Threading;
	using System.Threading.Tasks;
	
	namespace VideoSummarization
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
	

	            // Run the thumbnail job.
	            var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
	                                        @"C:\supportFiles\VideoThumbnail\config.json");
	
	            // Download the job output asset.
	            DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
	        }
	
	        static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
	        {
	            // Create an asset and upload the input media file to storage.
	            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	                "My Video Thumbnail Input Asset",
	                AssetCreationOptions.None);
	
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My Video Thumbnail Job");
	
	            // Get a reference to Azure Media Video Thumbnails.
	            string MediaProcessorName = "Azure Media Video Thumbnails";
	
	            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	            // Read configuration from the specified file.
	            string configuration = File.ReadAllText(configurationFile);
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
	                processor,
	                configuration,
	                TaskOptions.None);
	
	            // Specify the input asset.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
	
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

###Saída da miniatura de vídeo

[Saída da miniatura de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Links relacionados

[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0420_2016-->