---
title: Início Rápido da Configuração de Aplicativo do Azure com o Azure Functions | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com o Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 22ec05660682f000d8bc3b9780732d5adf9b5c24
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226701"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Início Rápido: Criar uma função do Azure com a Configuração de Aplicativo

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. É possível utilizá-lo para armazenar e gerenciar facilmente todas as configurações de aplicativo em um local separado do código. Este início rápido mostra como incorporar o serviço em um Azure Functions. 

Você pode usar qualquer editor de código para executar as etapas deste início rápido. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

![Início rápido local completo](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [Visual Studio 2017](https://visualstudio.microsoft.com/vs). Garanta que a carga de trabalho de **desenvolvimento do Azure** também seja instalada. Instale também as [ferramentas do Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version) mais recentes.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de configurações de aplicativo

1. Abra *Function1.cs* e adicione uma referência a um provedor de configuração .NET Core da Configuração de Aplicativo.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Atualize o método `Run` para usar a Configuração de Aplicativo chamando `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Testar a função localmente

1. Defina uma variável de ambiente nomeada **ConnectionString** e configure-a como a chave de acesso para o repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usa o Windows PowerShell, execute o comando a seguir:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usa macOS ou Linux, execute o comando a seguir:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar sua função, pressione F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas **principais (CLI) do Azure Functions**. Além disso, talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP.

3. Copie a URL da sua função da saída de tempo de execução do Azure Functions.

    ![Depuração de função do Início Rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função.

    ![Inicialização local da Função do Início Rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo repositório de configurações de aplicativos e utilizou-o com um Azure Functions. Para saber mais sobre como usar a Configuração de Aplicativo, vá para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
