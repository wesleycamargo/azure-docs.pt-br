<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Como: Codificar um ativo

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Obter um processador de mídia][].

Para conteúdo de mídia no servidor, você pode codificar o conteúdo com várias codificações e formatos de mídia usando o Codificador de Mídia do Azure. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Codificadores de terceiros estão disponíveis por meio do [Azure Marketplace][]. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres da [Predefinição do Codificador][], ou por meio de arquivos de configuração.

## Codificando para MP4

O método a seguir carrega um único ativo e cria um trabalho para codificar o ativo para MP4 usando a predefinição "H264 Broadband 720p”, que criará um único MP4 usando codificação H264 em resolução 720p:

     static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder) { //Create an encrypted asset and upload to storage. IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);
        // Declare a new job.

        IJob job = _context.Jobs.Create("My encoding job");

        // Get a reference to the Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder");

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

</code>

</pre>

## Codificando para Smooth Streaming

Para codificar um vídeo para smooth streaming, você tem duas opções:

-   Codificar diretamente para Smooth Streaming
-   Codificar para MP4 e, em seguida, converter em Smooth Streaming

Para codificar diretamente para Smooth Streaming, use o código mostrado acima, mas use uma das predefinições de codificador de Smooth Streaming. Para obter uma lista completa das predefinições do codificador, consulte [Cadeias de caracteres de predefinição de tarefa para o Codificador de Mídia do Azure][].

Para converter um MP4 para Smooth Streaming, use o Empacotador de Mídia do Azure. O Gerenciador de mídia do Azure não oferece suporte a predefinições de cadeias de caracteres, portanto, você deve especificar opções de configuração em XML. O XML necessário para converter MP4 em Smooth Streaming pode ser encontrado em [Predefinição de tarefa para o Empacotador de Mídia do Azure][]. Copie e cole o XML em um arquivo chamado MediaPackager\_MP4ToSmooth.xml em seu projeto. O código a seguir ilustra como converter um ativo MP4 em Smooth Streaming. O método abaixo usa um ativo existente e o converte.

<pre><code>private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
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

-   [Processar ativos com o SDK dos Serviços de Mídia para .NET][]
-   [Processar ativos com a API REST dos Serviços de Mídia][]

</p>
## Próximas etapas

Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico [Como verificar o andamento do trabalho com os Serviços de Mídia][].

  [Como: Obter um processador de mídia]: http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409
  [Azure Marketplace]: https://datamarket.azure.com/
  [Predefinição do Codificador]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Cadeias de caracteres de predefinição de tarefa para o Codificador de Mídia do Azure]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Predefinição de tarefa para o Empacotador de Mídia do Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx
  [Processar ativos com o SDK dos Serviços de Mídia para .NET]: http://msdn.microsoft.com/en-us/library/jj129580.aspx
  [Processar ativos com a API REST dos Serviços de Mídia]: http://msdn.microsoft.com/en-us/library/jj129574.aspx
  [Como verificar o andamento do trabalho com os Serviços de Mídia]: http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409
