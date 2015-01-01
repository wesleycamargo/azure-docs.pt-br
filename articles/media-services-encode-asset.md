<properties urlDisplayName="How to Encode an Asset" pageTitle="Como codificar um ativo para Serviços de Mídia - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Como: Codificar um ativo
Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Obter um processador de mídia].(../media-services-get-media-processor/).

Para conteúdo de mídia no servidor, você pode codificar o conteúdo com várias codificações e formatos de mídia usando o Codificador de Mídia do Azure. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Codificadores de terceiros estão disponíveis por meio do [Azure Marketplace][]. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres de[Predefinição do Codificador][] ou por meio de arquivos de configuração. 

##Codificando para conjunto adaptável de taxas de bit de MP4
Recomenda-se codificar seu arquivo mezanino para conjuntos adaptáveis de taxa de bits de MP4 e, em seguida, usar a embalagem Dinâmica para entregar o conteúdo. Para obter mais informações, consulte [Criando um trabalho de codificação com o SDK dos Serviços de Mídia para .NET].(http://msdn.microsoft.com/pt-br/library/azure/dn282273.aspx), [Empacotamento dinâmico](http://msdn.microsoft.com/pt-br/library/azure/jj889436.aspx), e[Conteúdo da entrega](http://msdn.microsoft.com/pt-br/library/azure/hh973618.aspx).

##Codificando para MP4
O método a seguir carrega um único ativo e cria um trabalho para codificar o ativo para MP4 usando a predefinição "H264 Broadband 720p", que criará um único MP4 usando codificação H264 em resolução 720p:
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
		// Create a task with the encoding details, using a string preset.
    	ITask task = job.Tasks.AddNew("My encoding task",
        	processor,
	        "H264 Broadband 720p",
        	_protectedConfig);
    
		// Specify the input asset to be encoded.
    	task.InputAssets.Add(asset);
    
		// Add an output asset to contain the results of the job. 
    	// This output is specified as AssetCreationOptions.None, which 
    	// means the output asset is in the clear (unencrypted). 
    	task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
		// Use the following event handler to check job progress.  
    	job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    
		// Launch the job.
    	job.Submit();
    
		// Optionally log job details. This displays basic job details
    	// to the console and saves them to a JobDetails-JobId.txt file 
    	// in your output folder.
    	LogJobDetails(job.Id);
    
		// Check job execution and wait for job to finish. 
    	Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    	progressJobTask.Wait();
    
		// If job state is Error, the event handling 
    	// method for job progress should log errors.  Here we check 
    	// for error state and exit if needed.
    	if (job.State == JobState.Error)
    	{
	        Console.WriteLine("\nExiting method due to job error.");
        	return job;
    	}
    
		// Perform other tasks. For example, access the assets that are the output of a job, 
    	// either by creating URLs to the asset on the server, or by downloading. 
    	return job;
	}

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
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
            	LogJobStop(job.Id);
            	break;
        	default:
	            break;
    	}
	}
</code></pre>
<h2>Codificando para Smooth Streaming</h2>
Para codificar um vídeo para smooth streaming, você tem duas opções:
<ul>
<li> Codificar diretamente para Smooth Streaming </li>
<li> Codificar para MP4 e, em seguida, converter em Smooth Streaming</li>
</ul>

Para codificar diretamente para Smooth Streaming, use o código mostrado acima, mas use uma das predefinições de codificador de Smooth Streaming. Para obter uma lista completa das predefinições do codificador, consulte [Cadeias de predefinição de tarefa para o codificador de mídia do Azure.](http://msdn.microsoft.com/pt-br/library/jj129582.aspx). 

Para converter um MP4 para Smooth Streaming, use o Empacotador de Mídia do Azure.  O Gerenciador de mídia do Azure não dá suporte a predefinições de cadeias de caracteres, portanto, você deve especificar opções de configuração em XML.  O XML necessário para converter MP4 em Smooth Streaming pode ser encontrado em[Predefinição de tarefa para o empacotador de mídia do Azure][]. Copie e cole o XML em um arquivo chamado MediaPackager_MP4ToSmooth.xml em seu projeto.O código a seguir ilustra como converter um ativo MP4 em Smooth Streaming.O método abaixo usa um ativo existente e o converte. 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

Para obter mais informações sobre como processar ativos, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129580.aspx">Processar ativos com o SDK dos Serviços de Mídia para .NET</a></li>
<li><a href="http://msdn.microsoft.com/pt-br/library/jj129574.aspx">Processar ativos com a API REST dos Serviços de Mídia</a></li>
</ul>

##Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico[Como verificar o andamento do trabalho com os Serviços de Mídia.](../media-services-check-job-progress/) .

[Azure Marketplace]: https://datamarket.azure.com/
[Predefinição do codificador]: http://msdn.microsoft.com/pt-br/library/dn619392.aspx
[Como: Obter uma instância do processador de mídia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Como: Carregar um ativo criptografado]:http://go.microsoft.com/fwlink/?LinkId=301733
[Como: Fornecer um ativo por download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Como verificar o andamento do trabalho]:http://go.microsoft.com/fwlink/?LinkId=301737
[Predefinição de tarefa para o Empacotador de Mídia do Azure]:http://msdn.microsoft.com/pt-br/library/windowsazure/hh973635.aspx

<!--HONumber=35_1-->
