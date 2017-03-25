---
title: "Desenvolver o Azure Functions com os Serviços de Mídia"
description: "Este tópico mostra como começar a desenvolver o Azure Functions com os Serviços de Mídia usando o portal do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Desenvolver o Azure Functions com os Serviços de Mídia
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico aborda como começar a desenvolver o Azure Functions com os Serviços de Mídia usando o portal do Azure. 

Você também pode implantar os Serviços de Mídia e Azure Functions existentes [aqui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) pressionando o botão **Implantar no Azure**. Esse repositório contém exemplos do Azure Functions que usam os Serviços de Mídia do Azure para mostrar os fluxos de trabalho relativos à ingestão de conteúdo diretamente do armazenamento de blobs, à codificação e à gravação do conteúdo de volta no armazenamento de blobs. Ele também inclui exemplos de como monitorar as notificações de trabalho por meio de Webhooks e Filas do Azure.

Você pode desenvolver suas funções com base nos exemplos [deste](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration) repositório. Este tópico mostra como começar a criação de Azure Functions que usam os Serviços de Mídia. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar sua primeira função, você precisará ter uma conta ativa do Azure. Se você ainda não tiver uma conta do Azure, [há contas gratuitas disponíveis](https://azure.microsoft.com/free/).

Se você pretende criar Azure Functions que executam ações em sua conta do AMS (Serviços de Mídia do Azure) ou escutar eventos enviados pelos Serviços de Mídia, crie uma conta do AMS conforme descrito [aqui](media-services-portal-create-account.md).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Crie um aplicativo de função conforme descrito [aqui](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app).

## <a name="create-a-function"></a>Criar uma função

Depois que o aplicativo de funções for implantado, você o encontrará entre os **Serviços de Aplicativos** do Azure Functions. 

1. Selecione seu aplicativo de funções e clique em **Nova Função**.
3. Escolha a linguagem **C#** e o cenário **Webhook + API**.
3. Selecione **GenericWebHook CSharp** (será executado sempre que receber uma solicitação webhook) ou **HttpTrigger CSharp** (será executado sempre que receber uma solicitação HTTP) e nomeie sua função.
4. Clique em **Criar**. 

## <a name="get-function-url"></a>Obter URL de função

Para disparar a execução de suas funções de uma ferramenta de teste de HTTP ou de outra janela do navegador, você precisará do valor da URL de função. 

![Configurações](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>Arquivos

A função do Azure está associada a arquivos de código e a outros arquivos descritos nesta seção. Por padrão, uma função está associada aos arquivos **function.json** e **run.csx**. Você precisará adicionar um arquivo **project.json**. O restante desta seção mostra as definições para esses arquivos.

![de entrada](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

O arquivo function.json define as associações de função e outras definições de configuração. O tempo de execução usa esse arquivo para determinar os eventos a serem monitorados, bem como para passar e retornar dados da execução da função. 

Aqui está um exemplo de arquivo **function.json**.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

O arquivo project.json contém dependências. Aqui está um exemplo de arquivo **function.json** que inclui as bibliotecas do AMS.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Este é o código C# para sua função. Para obter um exemplo de uma função de webhook, consulte [este](media-services-dotnet-check-job-progress-with-webhooks.md) tópico. 

Depois de definir a função, clique em **Executar**.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>Definir configurações do aplicativo de funções

Ao desenvolver funções dos Serviços de Mídia, é útil adicionar parâmetros que serão utilizados por suas funções à seção **Configurações do Aplicativo**. 

Por exemplo:

![Configurações](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>Próxima etapa

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia. Para saber mais, confira [Usar o Azure WebHooks para monitorar as notificações de trabalho dos Serviços de Mídia com o .NET](media-services-dotnet-check-job-progress-with-webhooks.md);   

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


