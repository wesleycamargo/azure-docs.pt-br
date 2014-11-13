<properties urlDisplayName="Encrypt Assets in Media Services" pageTitle="Como criptografar ativos em Servi&ccedil;os de M&iacute;dia - Azure" metaKeywords="" description="Saiba como usar a Prote&ccedil;&atilde;o do Microsoft PlayReady para criptografar um ativo nos Servi&ccedil;os de M&iacute;dia. Os exemplos de c&oacute;digo s&atilde;o escritos em C# e usam a SDK dos Servi&ccedil;os de M&iacute;dia para .NET. Os exemplos de c&oacute;digo s&atilde;o escritos em C# e usam a SDK dos Servi&ccedil;os de M&iacute;dia para .NET." metaCanonical="" services="media-services" documentationCenter="" title="Como: Proteger um ativo com a Prote&ccedil;&atilde;o do PlayReady" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="playready"></a>Como: Proteger um ativo com a Proteção do PlayReady

Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Verificar o andamento do trabalho][Como: Verificar o andamento do trabalho].

Nos Serviços de Mídia do Azure, você pode enviar um trabalho que se integra com a Proteção do Microsoft PlayReady para criptografar um ativo. O código desta seção utiliza vários arquivos de streaming de uma pasta de entrada, cria uma tarefa e os criptografa com a proteção do PlayReady.

O exemplo a seguir mostra como criar um trabalho simples para fornecer a proteção do PlayReady.

1.  Recupere os dados da configuração. Você pode obter um arquivo de configuração de exemplo no tópico [Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)][Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)].
2.  Carregue um arquivo de entrada MP4
3.  Converta o arquivo MP4 em um ativo de Smooth Streaming
4.  Criptografe o ativo com o PlayReady
5.  Envie o trabalho

O código de exemplo a seguir mostra como implementar as etapas:

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
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

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
        IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

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
                EventHandler<JobStateChangedEventArgs>(StateChanged);

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

Para obter mais informações sobre a proteção do PlayReady, consulte:

-   [Protegendo ativos com o Microsoft PlayReady][Protegendo ativos com o Microsoft PlayReady]
-   [Microsoft PlayReady][Microsoft PlayReady]

</p>
## Próximas etapas

Agora que você sabe como proteger ativos com os Serviços de Mídia, visite o tópico [Como gerenciar ativos (a página pode estar em inglês)][Como gerenciar ativos (a página pode estar em inglês)].

  [Como: Verificar o andamento do trabalho]: ../media-services-check-job-progress/
  [Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/hh973610.aspx
  [Protegendo ativos com o Microsoft PlayReady]: http://msdn.microsoft.com/pt-br/library/dn189154.aspx
  [Microsoft PlayReady]: http://www.microsoft.com/PlayReady/
  [Como gerenciar ativos (a página pode estar em inglês)]: ../media-services-manage-assets/
