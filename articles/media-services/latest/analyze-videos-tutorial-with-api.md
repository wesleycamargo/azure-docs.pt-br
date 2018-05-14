---
title: Analisar os vídeos com os Serviços de Mídia do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para analisar vídeos usando os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: a228443a787db2cc71a7700b978d8f52edb59e14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Tutorial: Analisar vídeos com os Serviços de Mídia do Azure 

Este tutorial mostra como analisar vídeos com os Serviços de Mídia do Azure. Há muitos cenários em que você talvez queira obter ideias profundas sobre vídeos gravados ou conteúdo de áudio. Por exemplo, para obter maior satisfação do cliente, as organizações podem executar processamento de fala para texto para converter gravações de suporte do cliente em um catálogo pesquisável com índices e painéis. Em seguida, podem obter ideias sobre seus negócios como uma lista de reclamações comuns, fontes de tais reclamações, etc.

Este tutorial mostra como:    

> [!div class="checklist"]
> * Iniciar o Azure Cloud Shell
> * Criar uma conta de Serviços de Mídia
> * Instalar a API de Serviços de Mídia
> * Configurar o aplicativo de exemplo
> * Examinar o código de exemplo em detalhes
> * Execute o aplicativo
> * Examinar a saída
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Examinar o código de exemplo em detalhes

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. Em primeiro lugar, você precisa obter um token e, em seguida, criar um objeto do **ClientCredential** retornado. No código clonado no início do artigo, o objeto **ArmClientCredential** é usado para obter o token.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um ativo de saída para armazenar o resultado de um trabalho 

O ativo de saída armazena o resultado do seu trabalho. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```
### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e um trabalho que analisa os vídeos

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado. 

#### <a name="transform"></a>Transformar

Ao criar um novo exemplo de **Transformação**, você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código acima. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. Neste exemplo, a predefinição **VideoAnalyzerPreset** é usada e o idioma ("en-US") é passado para o construtor. Esta predefinição permite extrair várias informações de áudio e vídeo de um vídeo. Você pode usar a predefinição **AudioAnalyzerPreset** se você precisa extrair as várias ideias de áudio de um vídeo. 

Ao criar uma **Transformação**, você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    Preset preset)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput(preset),
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de **Transformação** é a receita e um **Trabalho** é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída. Você pode especificar o local do seu vídeo usando: URLs HTTPS, URLs da SAS ou ativos que estão em sua conta de Serviço de Mídia. 

Neste exemplo, o vídeo de entrada é carregado de uma URL HTTPS especificada.  

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName, string jobName, string outputAssetName)
{
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });
            
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
                resourceGroupName,
                accountName,
                transformName,
                uniqueJobName,
                new Job
                {
                    Input = jobInput,
                    Outputs = jobOutputs,
                });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. Há diferentes opções para ser notificado sobre a conclusão do trabalho. A opção mais simples (que é mostrada aqui) é usar a sondagem. 

Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="download-the-result-of-the-job"></a>Faça o download do resultado do trabalho

 A função a seguir realiza os downloads dos resultados do ativo de saída para a pasta de "saída", para que você possa examinar os resultados do trabalho. 

```csharp
private static void DownloadResults(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
            resourceGroup,
            accountName,
            assetName,
            permissions: AssetContainerPermission.Read,
            expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
            );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);

    foreach (IListBlobItem blobItem in container.ListBlobs(null, true, BlobListingDetails.None))
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            blob.DownloadToFile(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName, 
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Pressione CTRL+F5 para executar o aplicativo *AnalyzeVideos*.

Quando executamos o programa, o trabalho produz miniaturas de cada face que encontrar no vídeo. Ele também produz o arquivo insights.json.

## <a name="examine-the-output"></a>Examinar a saída

O arquivo de saída de análise de vídeos é chamado insights.json. Esse arquivo contém informações sobre o vídeo. Você pode encontrar uma descrição dos elementos encontrados no arquivo json no artigo [Inteligência de mídia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente. Use a ferramenta **CloudShell**.

No **CloudShell**, execute o seguinte comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao trabalhar com aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
