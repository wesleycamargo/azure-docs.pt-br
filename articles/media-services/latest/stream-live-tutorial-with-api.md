---
title: Transmitir ao vivo com Serviços de Mídia do Microsoft Azure v3 usando .NET Core | Microsoft Docs
description: Este tutorial orienta-o pelas etapas de transmissão ao vivo com o Serviços de Mídia v3 usando o .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: f02820f8382268dfedeee8d07de2438660e5e33e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236868"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Transmitir ao vivo com Serviços de Mídia do Microsoft Azure v3 usando .NET Core

Nos Serviços de Mídia, [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pelo processamento do conteúdo de transmissão ao vivo. Um LiveEvent fornece um ponto de extremidade de entrada (URL de entrada) que você fornece a um codificador dinâmico. O LiveEvent recebe fluxos de entrada ao vivo do codificador dinâmico e o disponibiliza para streaming por meio de um ou mais [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). O LiveEvents também fornece um ponto de extremidade de versão prévia (URL de versão prévia) usado para visualizar e validar o fluxo antes de processamento e entrega adicionais. Este tutorial mostra como usar o .NET Core para criar um tipo de **passagem** de um evento ao vivo. 

> [!NOTE]
> Certifique-se de revisar [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md) antes de continuar. 

Este tutorial mostra como:    

> [!div class="checklist"]
> * Criar uma conta de Serviços de Mídia
> * Instalar a API de Serviços de Mídia
> * Configurar o aplicativo de exemplo
> * Examinar o código que executa a transmissão ao vivo
> * Assista ao evento com o [Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) em http://ampdemo.azureedge.net
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

* Instalar Visual Studio Code ou Visual Studio
* Uma câmera ou um dispositivo (como laptop) usado para transmitir um evento.
* Um codificador ao vivo local que converte sinais da câmera em fluxos enviados para o serviço de transmissão ao vivo dos Serviços de Mídia. O fluxo deve estar no formato **RTMP** ou **Smooth Streaming**.

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

A amostra de transmissão ao vivo está localizada na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> Esta amostra usa o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, finalizará vários LiveEvents em sua conta. <br/>
> Certifique-se de parar os LiveEvents em execução. Caso contrário, você será **cobrado**!

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que executa a transmissão ao vivo

Esta seção examina as funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

A amostra cria um sufixo exclusivo para cada recurso, de modo que não ocorra conflitos de nome se executar a amostra várias vezes sem limpeza.

> [!IMPORTANT]
> Esta amostra usa o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, finalizará vários LiveEvents em sua conta. <br/>
> Certifique-se de parar os LiveEvents em execução. Caso contrário, você será **cobrado**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Criar um evento ao vivo

Esta seção mostra como criar um tipo de **passagem** do LiveEvent (LiveEventEncodingType definido como Nenhum). Se você quer criar um LiveEvent habilitado para codificação ao vivo, defina LiveEventEncodingType como Básico. 

Outras opções que talvez você queira especificar ao criar o evento ao vivo são:

* Local dos Serviços de Mídia 
* O protocolo de streaming para o Evento ao Vivo (atualmente, os protocolos RTMP e Smooth Streaming t~em suporte)
       
    Não é possível alterar a opção de protocolo enquanto o LiveEvent ou os LiveOutputs associados estiverem em execução. Se você precisar de protocolos diferentes, crie um LiveEvent separado para cada protocolo de streaming.  
* Restrições de IP sobre a ingestão e versão prévia. É possível definir os endereços IP que tenham permissão para ingerir um vídeo neste LiveEvent. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').
    
    Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

Ao criar o evento, é possível especificar para iniciá-lo automaticamente. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>Obter URLs de ingestão

Depois que o canal é criado, você pode obter URLs de ingestão que você fornecerá ao codificador ao vivo. O codificador usa essas URLs para gerar entrada de um fluxo ao vivo.


```csharp
// Get the input endpoint to configure the on premise encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on premise encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Obter a URL de versão prévia

Use o previewEndpoint para visualizar e verificar se a entrada do codificador está realmente sendo recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está fluindo para a URL de versão prévia antes de continuar!

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Criar e gerenciar LiveEvents e LiveOutputs

Uma vez que o fluxo está fluindo para o LiveEvent, será possível iniciar o evento de streaming criando um Asset, LiveOutput e StreamingLocator. Isso arquivará o fluxo e o disponibilizará aos espectadores por meio do StreamingEndpoint. 

#### <a name="create-an-asset"></a>Criar um ativo

Criar um Ativo para o LiveOutput usar.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>Criar um LiveOutput

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>Criar um StreamingLocator

> [!NOTE]
> Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Limpar recursos na conta dos Serviços de Mídia

Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

* Pare de enviar o fluxo por push por meio do codificador.
* Pare o LiveEvent. Depois que o LiveEvent estiver parado, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
* É possível parar o StreamingEndpoint, a menos que você queira continuar fornecendo o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o LiveEvent estiver no estado parado, ele não incorrerá em nenhum encargo.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Assistir ao evento

Para assistir ao evento, copie a URL de streaming que você obteve quando executou o código descrito em [Criar um StreamingLocator](#create-a-streaminglocator) e use um player de sua escolha. É possível usar o [Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) para testar o stream em http://ampdemo.azureedge.net. 

O evento ativo é convertido automaticamente em conteúdo sob demanda quando é interrompido. Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo. Não será possível excluir um ativo se este for usado por um evento; o evento deve ser excluído primeiro. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente. Use a ferramenta **CloudShell**.

No **CloudShell**, execute o seguinte comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o LiveEvent executando incorre em custos de cobrança. Esteja ciente de que, se o projeto/programa falhar ou for fechado por qualquer motivo, isso poderá deixar o LiveEvent executando em um estado de cobrança.

## <a name="next-steps"></a>Próximas etapas

[Transmissão de arquivos](stream-files-tutorial-with-api.md)

