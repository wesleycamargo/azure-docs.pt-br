<properties 
	pageTitle="Como codificar um ativo usando o Media Encoder Standard" 
	description="Este tópico mostra como usar o .NET para codificar um ativo com o Media Encoder Standard." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="05/03/2016"
	ms.author="juliako;anilmur"/>


#Como codificar um ativo usando o Media Encoder Standard

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace.

Este tópico mostra como usar o .NET para codificar seus ativos com o MES (Media Encoder Standard). O Media Encoder Standard é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-manage-origins.md#scale_streaming_endpoints).

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]O MES produz um arquivo de saída com um nome que contém os primeiros 32 caracteres do nome do arquivo de entrada. O nome é baseado no que é especificado no arquivo de predefinição. Por exemplo, "FileName": "{Basename}\_{Index}{Extension}". {Basename} é substituído pelos primeiros 32 caracteres do nome do arquivo de entrada.

###Formatos de MES

[Formatos e codecs](media-services-media-encoder-standard-formats.md)

###Predefinições de MES

O Media Encoder Standard é configurado usando um dos codificadores predefinidos descritos [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadados de entrada e saída

Ao codificar um ativo (ou ativos) de entrada usando MES, você obtém um ativo de saída na conclusão bem-sucedida da tarefa de codificação. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc., com base na predefinição de codificação utilizada.

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: <asset_id>\_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml), em que <asset_id> é o valor de AssetId do ativo de entrada. O esquema desse XML de metadados de entrada é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: <source_file_name>\_manifest.xml (por exemplo, BigBuckBunny\_manifest.xml). O esquema desse XML de metadados de saída é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se desejar examinar qualquer um dos dois arquivos de metadados, você poderá criar um localizador SAS e baixe o arquivo em seu computador local. É possível encontrar um exemplo de como criar um localizador SAS e baixar um arquivo usando as Extensões do SDK do .NET para os Serviços de Mídia.

##Baixar exemplo

Obtenha e execute um exemplo [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Exemplo

O exemplo de código a seguir usa o SDK .NET dos Serviços de Mídia para executar as seguintes tarefas:

- Crie um trabalho de codificação.
- Obtenha uma referência para o Media Encoder Standard.
- Especifique o uso da predefinição "720p com várias taxas de bits H264". Você pode ver todas as predefinições [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Você também pode examinar o esquema que essas predefinições devem seguir [aqui](https://msdn.microsoft.com/library/mt269962.aspx) neste tópico.
- Adicione uma única tarefa de codificação para o trabalho. 
- Especifique o ativo de entrada a ser codificado.
- Crie um ativo de saída que conterá o ativo codificado.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Enviar o trabalho.
		
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


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Consulte também 

[Como gerar miniatura usando o codificador de mídia padrão com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md) [Visão geral de codificação dos Serviços de Mídia](media-services-encode-asset.md)

<!---HONumber=AcomDC_0518_2016-->