<properties 
	pageTitle="Como codificar um ativo usando o Codificador de Mídia do Azure" 
	description="Saiba como usar o Codificador de Mídia do Azure para codificar conteúdo de mídia nos Serviços de Mídia. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Como codificar um ativo usando o Codificador de Mídia do Azure

Este artigo faz parte da série do [vídeo de serviços de mídia no fluxo de trabalho de demanda](media-services-video-on-demand-workflow.md). 

## Visão geral
Para fornecer vídeo digital pela internet, você deve compactar a mídia. Os arquivos de vídeo digital são muito grandes e podem ser muito grandes para entregar pela internet ou para dispositivos de seus clientes para exibir corretamente. A codificação é o processo de compactação de vídeo e áudio para que seus clientes possam exibir sua mídia.

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres predefinidas para seu codificador ou usando arquivos de configuração predefinidos. Para ver os tipos de predefinições disponíveis, consulte Predefinições de tarefa para os serviços de mídia do Azure. Se você usou um codificador de terceiros, você deve [validar seus arquivos](https://msdn.microsoft.com/pt-br/library/azure/dn750842.aspx).

Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](https://msdn.microsoft.com/pt-br/library/azure/jj889436.aspx).


## Criar um trabalho com uma única tarefa de codificação 

Quando estiver codificando com o Codificador de Mídia do Azure, você pode usar as predefinições de tarefa de configuração especificadas [aqui](https://msdn.microsoft.com/pt-br/library/azure/dn619389.aspx).

### Use o SDK dos serviços de mídia para o .NET  

O seguinte método **EncodeToAdaptiveBitrateMP4Set** cria um trabalho de codificação e adiciona uma única tarefa de codificação para o trabalho. A tarefa usa "Azure Media Encoder" para codificar como "H264 Adaptive Bitrate MP4 Set 720p". 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


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

### Use o SDK dos serviços de mídia para as extensões do .NET

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

## Criar um trabalho com tarefas encadeadas 

Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar diferentes processadores de mídia. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, desempenhando uma sequência linear de tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos**ITask** são criados em um único objeto **IJob**.

>[AZURE.NOTE] Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.

O seguinte método **CreateChainedTaskEncodingJob** cria um trabalho que contém duas tarefas encadeadas. Como resultado, o método retorna um trabalho que contém dois ativos de saída.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


## Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico [Como verificar o andamento do trabalho com os Serviços de Mídia](media-services-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Predefinição do codificador]: http://msdn.microsoft.com/library/dn619392.aspx
[Como: obter uma instância do processador de mídia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Como: carregar um ativo criptografado]:http://go.microsoft.com/fwlink/?LinkId=301733
[Como: fornecer um ativo por download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Como verificar o andamento do trabalho]:http://go.microsoft.com/fwlink/?LinkId=301737
[Predefinição de tarefa para o empacotador de mídia do Azure]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=45--> 
