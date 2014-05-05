<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Criptografar ativos nos Serviços de Mídia" pageTitle="Como criptografar ativos nos Serviços de Mídia - Azure" metaKeywords="" description="Saiba como usar a Proteção do Microsoft PlayReady para criptografar um ativo nos Serviços de Mídia. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET. Os exemplos de código são escritos em C# e usam o SDK dos Serviços de Mídia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="Como codificar um ativo com a Proteção do PlayReady" authors="migree" solutions="" manager="" editor="" />





<h1><a name="playready"></a>Como proteger um ativo com a Proteção do PlayReady</h1>

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior foi [Como verificar o andamento do trabalho](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409)

Nos Serviços de Mídia do Azure, você pode enviar um trabalho que se integra com a Proteção do Microsoft PlayReady para criptografar um ativo. O código desta seção utiliza vários arquivos de streaming de uma pasta de entrada, cria uma tarefa e os criptografa com a proteção do PlayReady. 

O exemplo a seguir mostra como criar um trabalho simples para fornecer a proteção do PlayReady.

   1. Recupere os dados da configuração. Você pode obter um arquivo de configuração de exemplo no tópico [Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/hh973610.aspx).
   2. Carregue um arquivo de entrada MP4
   3. Converta o arquivo MP4 em um ativo de Smooth Streaming
   4. Criptografe o ativo com PlayReady
   5. Envie o trabalho

O código de exemplo a seguir mostra como implementar as etapas:

<pre><code>
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Create a storage-encrypted asset and upload the MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declare a new job to contain the tasks.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Set up the first task. 

    // Read the task configuration data into a string. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Get a media processor instance
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Create a task with the conversion details, using the configuration data 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Specify the input asset to be converted.
    task.InputAssets.Add(asset);

    // Add an output asset to contain the results of the job. We do not need 
    // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
    // parameter to false. 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Set up the encryption task. 

    // Read the configuration data into a string. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Get a media processor instance
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Create a second task, specifying a task name, the media processor, and configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Add the input asset, which is the smooth streaming output asset from the first task. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Add an output asset to contain the results of the job. Persist the output by setting 
    // the shouldPersistOutputOnCompletion param to true.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Use the following event handler to check job progress. 
    job.StateChanged += new
            EventHandler&lt;JobStateChangedEventArgs&gt;(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
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

</code></pre>
Para obter mais informações sobre a Proteção do PlayReady, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/pt-br/library/dn189154.aspx">Protegendo ativos com o Microsoft PlayReady</a></li>
<li><a href="http://www.microsoft.com/PlayReady/">Microsoft PlayReady</a></li>
</ul>

<h2>Próximas etapas</h2>
Agora que você sabe como proteger ativos com os Serviços de Mídia, visite o tópico [Como gerenciar ativos (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409).

