<properties
	pageTitle="Detectar movimentos com o Azure Media Analytics"
	description="O MP (processador de mídia) Azure Media Motion Detector permite a identificação eficiente de seções de interesse em um vídeo longo e rotineiro."
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
 
# Detectar movimentos com o Azure Media Analytics

##Visão geral

O MP (processador de mídia) **Azure Media Motion Detector** permite a identificação eficiente de seções de interesse em um vídeo longo e rotineiro. A detecção de movimento pode ser usada em sequências de imagens estáticas para identificar seções do vídeo onde ocorrem movimentos. Ela gera um arquivo JSON contendo metadados com carimbos de hora e a região delimitadora onde o evento ocorreu.

Essa tecnologia, destinada à segurança de feeds de vídeo, é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como mudanças de iluminação e sombras. Isso permite a geração de alertas de segurança por meio de feeds da câmera, sem gerar incontáveis eventos irrelevantes, além de permitir também a extração de momentos de interesse dos vídeos de vigilância extremamente longos.

No momento, o MP **Azure Media Motion Detector** está em versão de Visualização.

Este tópico fornece detalhes sobre o **Azure Media Motion Detector** e mostra como usá-lo com o SDK dos Serviços de Mídia para .NET

##Arquivos de entrada do Motion Detector

Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

##Arquivos de saída do Motion Detector

Um trabalho de detecção de movimento retornará um arquivo JSON no ativo de saída, que descreve os alertas de movimento, e suas categorias, no vídeo. O arquivo conterá informações sobre a hora e a duração do movimento detectado no vídeo.

Atualmente, a detecção de movimento só dá suporte à categoria de movimento genérico, conhecida como ***tipo 2*** na saída.

As coordenadas X e Y e os tamanhos serão listados usando um número de ponto flutuante normalizado entre 0,0 e 1,0. Multiplique isso pela resolução de altura e largura do vídeo a fim de obter a caixa delimitadora da região do movimento detectado.

Cada saída é dividida em fragmentos e subdividida em intervalos a fim de definir os dados no vídeo. As durações do fragmento não precisam ser iguais e podem abranger longas durações quando não houver qualquer detecção de movimento.

A API do Motion Detector fornecerá indicadores quando houver objetos em movimento em um vídeo fixo em segundo plano (por exemplo, um vídeo de vigilância). O Motion Detector é treinado para reduzir alarmes falsos, como mudanças de iluminação e de sombra. As limitações atuais dos algoritmos incluem vídeos de visão noturna, objetos semitransparentes e objetos pequenos.

###<a id="output_elements"></a>Elementos do arquivo JSON de saída

A tabela a seguir descreve os elementos do arquivo JSON de saída:

Elemento|Descrição
---|---
Versão|Refere-se à versão da API de Vídeo.
Escala de tempo|"Tiques" por segundo do vídeo.
Deslocamento|A diferença de tempo para carimbos de hora em “tiques”. Na versão 1.0 das APIs de Vídeo, sempre será 0. Em cenários futuro para os quais oferecemos suporte, esse valor poderá ser alterado.
Taxa de quadros|Quadros por segundo do vídeo.
Largura, Altura|Refere-se à largura e à altura do vídeo em pixels.
Iniciar|O carimbo de hora inicial em "tiques".
Duração|A duração do evento, em "tiques".
Intervalo|O intervalo de cada entrada no evento, em "tiques".
Eventos|Cada fragmento de evento contém o movimento detectado dentro dessa duração.
Tipo|Na versão atual, essa opção sempre será “2” para movimentos genéricos. Esse rótulo dá a flexibilidade às APIs de Vídeo para categorizar o movimento em futuras versões.
RegionID|Conforme explicado acima, isso sempre será 0 nesta versão. Esse rótulo oferece à API de Vídeo a flexibilidade de encontrar o movimento em várias regiões em versões futuras.
Regiões|Refere-se à área no vídeo na qual você se preocupa com o movimento. Na versão atual das APIs de Vídeo, não é possível especificar uma região. Em vez disso, toda a superfície do vídeo será a área de movimento que será detectada.<br/>- A ID representa a área da região – nesta versão, existe apenas uma, a ID 0. <br/>- O retângulo representa a forma da região na qual você se preocupa com o movimento. Nesta versão, sempre será retângulo. <br/>- A região tem dimensões em X, Y, Largura e Altura. As coordenadas X e Y representam as coordenadas XY do lado superior esquerdo da região em uma escala normalizada de 0,0 a 1,0. A largura e a altura representam o tamanho da região em uma escala normalizada de 0,0 a 1,0. Na versão atual, X, Y, Largura e Altura sempre serão fixas em 0, 0 e 1, 1.<br/>- Fragmentos Os metadados são agrupados em segmentos diferentes chamados de fragmentos. Cada fragmento contém um início, uma duração, um número de intervalo e evento(s). Um fragmento sem eventos significa que nenhum movimento foi detectado durante essa hora de início e duração.
Colchetes|Cada colchete representa um intervalo no evento. Colchetes vazios para esse intervalo significam que nenhum movimento foi detectado.
 

##Configuração de tarefa (predefinição)

Quando você criar uma tarefa com o **Azure Media Motion Detector**, deverá especificar uma predefinição de configuração. No momento, não é possível definir opções na predefinição de configuração do Azure Media Motion Detector. Veja a seguir a predefinição de configuração mínima que você deve fornecer.

	{"version":"1.0"}

##Exemplo de vídeos e saídas do Motion Detector

###Exemplo com movimento real

[Exemplo com movimento real](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###Saída em JSON

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###Exemplo com falsos positivos

[Exemplo com falsos positivos (pequenas alterações):](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###Saída em JSON

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##Limitações

- Os formatos de vídeo de entrada com suporte incluem MP4, MOV e WMV.
- A detecção de movimento é otimizada para vídeos estáticos em segundo plano. O algoritmo se concentra na redução de alarmes falsos, como mudanças de iluminação e sombras.
- Talvez algum movimento não seja detectado devido a desafios técnicos; por exemplo, vídeos de visão noturna, objetos semitransparentes e objetos pequenos.


## Exemplo de código

O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
1. Criar um trabalho com uma tarefa de detecção de movimento em vídeo baseada em um arquivo de configuração que contém a predefinição de JSON a seguir. 
					
		{
		    "version": "1.0"
		}

1. Baixar os arquivos JSON de saída.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Links relacionados

[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0420_2016-->