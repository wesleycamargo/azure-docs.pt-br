---
title: Azure Content Moderator – moderação de vídeo | Microsoft Docs
description: Use moderação de vídeo para verificar possível conteúdo para adulto.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364590"
---
# <a name="video-moderation"></a>Moderação de vídeo

Hoje, visualizadores online geram bilhões de exibições de vídeo em sites da mídia social regionais e populares, e esse número está aumentando. Aplicando serviços com base de aprendizado de máquina para prever possível conteúdo adulto e estimulante, você reduz o custo de seus esforços de moderação.

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Inscreva-se para o processador de mídia do Content Moderator (versão prévia pública)

### <a name="create-a-free-azure-account"></a>Criar uma conta gratuita do Azure

[Comece aqui](https://azure.microsoft.com/free/) para criar uma conta gratuita do Azure se você ainda não tiver uma.

### <a name="create-an-azure-media-services-account"></a>Criar uma conta de Serviços de Mídia do Azure

A funcionalidade de vídeo do Content Moderator está disponível como um **processador de mídia** de versão prévia pública no AMS (Serviços de Mídia do Azure) sem custos.

[Crie uma conta de Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) na sua assinatura do Azure.

### <a name="get-azure-active-directory-credentials"></a>Obter credenciais do Azure Active Directory

   1. Leia o [artigo do portal dos Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) para aprender a usar o portal do Azure para obter suas credenciais de autenticação do Azure AD.
   1. Leia o [artigo do .NET de Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) para aprender a usar suas credenciais do Azure Active Directory com o SDK do .NET.

   > [!NOTE]
   > O código de exemplo neste início rápido usa o método de **autenticação da entidade de serviço** método descrito em ambos os artigos.

Depois de obter as credenciais de AMS, há duas maneiras de experimentar o processador de mídia do Content Moderator.

## <a name="use-azure-media-services-explorer"></a>Usar o Gerenciador de Serviços de Mídia do Azure

Use o [Gerenciador de AMS (Serviços de Mídia do Azure)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) interativo para procurar sua conta de AMS, fazer upload de vídeos e examinar com o processador de mídia do Content Moderator. [Baixe e instale-o](https://github.com/Azure/Azure-Media-Services-Explorer/releases) do GitHub e [procure o código-fonte](http://github.com/Azure/Azure-Media-Services-Explorer) para saber mais sobre como usar o SDK de AMS.

![Gerenciador de Serviços de Mídia do Azure com Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Início rápido do .NET com Visual Studio e C#

1. Adicione um novo projeto de **Aplicativo de console (.NET Framework)** à sua solução.

   No código de exemplo, dê ao projeto o nome de **VideoModeration**.

1. Selecione esse projeto como o único projeto de inicialização para a solução.

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Instale os seguintes pacotes do NuGet disponíveis em [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>Atualizar o programa usando instruções

Modifique as instruções de uso do programa.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Inicialize as configurações específicas do aplicativo

Adicione os seguintes campos estáticos à classe **Programa** em Program.cs.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Criar um arquivo predefinido (json)

Crie um arquivo JSON no diretório atual com o número de versão.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Adicione o código a seguir ao método main

O método main primeiro cria um Contexto de Mídia do Azure e, em seguida, um Contexto de Armazenamento do Azure caso seus vídeos estejam no Armazenamento de Blobs.
O código restante examina um vídeo de uma pasta local, blob ou vários blobs dentro de um contêiner de armazenamento do Azure.
Você pode tentar todas as opções comentando nas outras linhas de código.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Adicionar o código para criar um Contexto de Mídia do Azure

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adicionar o código para criar um Contexto de Armazenamento do Azure
Você pode usar o Contexto de Armazenamento, criado com base nas suas credenciais de Armazenamento, para acessar o Armazenamento de Blobs.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adicione o código para criar Ativos de Mídia do Azure usando o blob e o arquivo local
O processador de mídia do Content Moderator executa trabalhos em **Ativos** dentro da plataforma de Serviços de Mídia do Azure.
Esses métodos criam os Ativos de um arquivo local ou um blob associado.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adicione o código para verificar uma coleção de vídeos (como blobs) dentro de um contêiner

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adicione o método para executar o Trabalho do Content Moderator

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
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
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Adicione algumas funções auxiliares

Esses métodos baixam o arquivo de saída do Content Moderator (JSON) do ativo dos Serviços de Mídia do Azure e ajudam a acompanhar o status do trabalho moderação para que o programa possa registrar em log um status de execução para o console.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Execute o programa e examine a saída

Depois que o trabalho de Moderação de Conteúdo estiver concluído, analise a resposta JSON. Consiste nestes elementos:

- Resumo de informações de vídeo
- **Capturas** como "**fragmentos**"
- **Quadros-chave** como "**eventos**" com um sinalizador **reviewRecommended "(= true ou false)"** com base em pontuações **Adulto** e **Estimulante**
- **start**, **duration**, **totalDuration** e **timestamp** estão em "tiques". Divida por **escala de tempo** para obter o número de segundos.
 
> [!NOTE]

> - `adultScore` representa a presença em potencial e a pontuação de previsão do conteúdo que pode ser considerado sexualmente explícito ou adulto em determinadas situações.
> - `racyScore` representa a presença em potencial e a pontuação de previsão do conteúdo que pode ser considerado sexualmente sugestivo ou maduro em determinadas situações.
> - `adultScore` e `racyScore` estão entre 0 e 1. Quanto maior a pontuação, maior o modelo estará prevendo que a categoria pode ser aplicável. Essa versão prévia se baseia em um modelo estatístico, em vez de em resultados codificados manualmente. É recomendável testar com o seu próprio conteúdo para determinar como cada categoria se alinha aos seus requisitos.
> - `reviewRecommended` é true ou false, dependendo dos limites da pontuação interna. Os clientes devem avaliar se desejam usar esse valor ou escolher limites personalizados com base nas políticas de conteúdo.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>Próximas etapas

Saiba como gerar [análises de vídeo](video-reviews-quickstart-dotnet.md) usando sua saída de moderação.

Adicione [moderação de transcrição](video-transcript-moderation-review-tutorial-dotnet.md) às suas análise de vídeo.

Confira o tutorial detalhado sobre como compilar uma [solução de moderação de vídeo e transcrição completa](video-transcript-moderation-review-tutorial-dotnet.md).

[Baixe a solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido do Content Moderator para .NET.
