---
title: Como executar as funções duráveis como WebJobs - Azure
description: Saiba como codificar e configurar funções durável para executar em trabalhos Web usando o SDK do WebJobs.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: df12639aaafaf3df7ae2b755d635d4fba83d846e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648645"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar as funções duráveis como WebJobs

Por padrão, as funções duráveis usa o Azure Functions runtime orquestrações de host. No entanto, pode haver alguns cenários em que você precisa de mais controle sobre o código que escuta eventos. Este artigo mostra como implementar sua orquestração usando o SDK de WebJobs. Para ver uma comparação mais detalhada entre funções e trabalhos Web, consulte [comparar Functions e WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) e extensão de [Funções Duráveis](durable-functions-overview.md) baseiam-se no [SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md). O host de trabalho no SDK do WebJobs é o tempo de execução no Azure Functions. Se você precisar controlar o comportamento de maneiras não possíveis no Azure Functions, você pode desenvolver e executar funções duráveis, usando o SDK de WebJobs por conta própria.

Na versão 3.x do SDK do WebJobs, o host é uma implementação de `IHost`e na versão 2. x que você use o `JobHost` objeto.

O exemplo de funções duráveis encadeamento está disponível em uma versão do SDK de WebJobs 2. x: Baixe ou clone a [repositório funções duráveis](https://github.com/azure/azure-functions-durable-extension/)e vá para o *amostras\\webjobssdk\\encadeamento* pasta.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com os conceitos básicos do SDK do WebJobs, desenvolvimento de bibliotecas de classe C# para funções do Azure Functions e as funções duráveis. Se você precisar obter uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar sua primeira função usando o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções duráveis](durable-functions-sequence.md)

Para completar as etapas neste artigo:

* [Instale o Visual Studio 2017 versão 15.6 ou posterior](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure**.

  Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho, selecionando **ferramentas** > **obter ferramentas e recursos**.

  (Você pode usar o [código do Visual Studio](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio Code.)

* Instale e execute o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) 5.2 ou posterior. Uma alternativa é atualizar o arquivo *App. config* com uma cadeia de caracteres de conexão de armazenamento do Microsoft Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Este artigo explica como desenvolver um projeto de 2. x do SDK do WebJobs (equivalente a versão do Azure Functions 1. x). Para obter informações sobre versão 3. x, consulte [SDK do WebJobs 3. x](#webjobs-sdk-3x) posteriormente neste artigo.

## <a name="create-a-console-app"></a>Criar um aplicativo de console

Para executar as funções duráveis como WebJobs, crie primeiro um aplicativo de console. Um projeto do SDK do WebJobs é apenas um projeto de aplicativo de console com os pacotes do NuGet apropriados instalados.

No Visual Studio **novo projeto** caixa de diálogo, selecione **área de trabalho clássica do Windows** > **aplicativo de Console (.NET Framework)**. No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

O Visual Studio também tem um modelo de projeto trabalho Web, que pode ser usado, selecionando **Cloud** > **Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Você precisará dos pacotes do NuGet para o SDK do WebJobs, associações de núcleo, a estrutura do registro em log e a extensão de tarefa durável. Aqui estão **Package Manager Console** comandos para esses pacotes, com os números de versão estável mais recente a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Você também precisa de provedores de log. Os comandos a seguir instalam o provedor do Azure Application Insights e o `ConfigurationManager`. O `ConfigurationManager` permite que você obtenha a chave de instrumentação do Application Insights de configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Tendo criado o aplicativo de console e instalar os pacotes do NuGet é necessário, você está pronto para usar as funções duráveis. Você pode fazer isso por meio de código JobHost.

Para usar a extensão de Funções Duráveis, chame `UseDurableTask` no `JobHostConfiguration` do objeto em seu `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no `DurableTaskExtension` de objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método também é o local para configurar os provedores de log. O exemplo a seguir configura o console e os provedores do Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funções

As funções duráveis no contexto de trabalhos Web difere um pouco do funções duráveis no contexto do Azure Functions. É importante estar ciente das diferenças, conforme você escreve seu código.

O SDK do WebJobs não oferece suporte para os seguintes recursos do Azure Functions:

* [Atributo FunctionName](#functionname-attribute)
* [Gatilho HTTP](#http-trigger)
* [API de gerenciamento de funções HTTP duráveis](#http-management-api)

### <a name="functionname-attribute"></a>Atributo FunctionName

Em um projeto do SDK do WebJobs, o nome do método de uma função é o nome da função. O `FunctionName` atributo é usado somente no Azure Functions.

### <a name="http-trigger"></a>Gatilho HTTP

O SDK do WebJobs não tem um gatilho HTTP. O cliente de orquestração do projeto de exemplo usa um gatilho de timer:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de Gerenciamento HTTP

Por não haver nenhum gatilho HTTP, o SDK do WebJobs não tem [API de gerenciamento HTTP](durable-functions-http-api.md).

Em um projeto do SDK de WebJobs, você pode chamar métodos no objeto de cliente de orquestração, em vez de ao enviar solicitações HTTP. Os métodos a seguir correspondem a três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função do cliente de orquestração no projeto de exemplo inicia a função de orquestrador e, em seguida, entra em um loop que chama `GetStatusAsync` cada 2 segundos:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Execute o exemplo

Você tem funções duráveis configurados para serem executados como um trabalho Web, e agora você tem uma compreensão de como isso será diferente da execução de funções duráveis como autônomo do Azure Functions. Neste ponto, observar seu funcionamento em um exemplo pode ser útil.

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK do WebJobs localmente e implantá-lo em um Webjob do Azure, consulte [começar com o SDK do WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o Emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. Se você quiser ver os logs no Application Insights quando você executa o projeto localmente:

     a. Criar um recurso do Application Insights e usar o **geral** tipo de aplicativo para ele.

    b. Salvar a chave de instrumentação no arquivo *App.config*.

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Criar um aplicativo Web e uma conta de armazenamento.

1. No aplicativo web, salvar a cadeia de caracteres de conexão de armazenamento em uma configuração de aplicativo chamada `AzureWebJobsStorage`.

1. Criar um recurso do Application Insights e usar o **geral** tipo de aplicativo para ele.

1. Salvar a chave de instrumentação em um configuração de aplicativo chamada `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Implantar como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

Este artigo explica como desenvolver um projeto de 2.x do SDK de WebJobs. Se você estiver desenvolvendo um [SDK de WebJobs 3.x](../../app-service/webjobs-sdk-get-started.md) projeto, esta seção o ajuda a entender as diferenças.

A principal alteração apresentada é o uso do .NET Core em vez do .NET Framework. Para criar um projeto do SDK de WebJobs 3. x, as instruções são os mesmos, com as seguintes exceções:

1. Criar um aplicativo de console do aplicativo .NET Core. No Visual Studio **novo projeto** caixa de diálogo, selecione **.NET Core** > **aplicativo de Console (.NET Core)**. O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.x`.

1. Escolha a versão de lançamento do SDK de WebJobs 3.x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Defina a cadeia de caracteres de conexão de armazenamento e a chave de instrumentação do Application Insights em um *appSettings. JSON* arquivo, usando a estrutura de configuração do .NET Core. Aqui está um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Alterar o `Main` código do método para fazer isso. Aqui está um exemplo:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SDK do WebJobs, consulte [como usar o SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md).
