---
title: Como executar funções duráveis como WebJobs - Azure
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
ms.openlocfilehash: c1a778a006c7743b6ea19062d79cff3cfeaba3d8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713261"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar funções duráveis como WebJobs

[Azure Functions](../functions-overview.md) e extensão de [Funções Duráveis](durable-functions-overview.md) baseiam-se no [SDK do WebJobs](../../app-service/webjobs-create.md). O `JobHost` SDK do WebJobs é o tempo de execução no Azure Functions. Se você precisa controlar `JobHost` comportamento de maneiras que não são possíveis no Azure Functions, você pode desenvolver e executar funções duráveis usando o SDK do WebJobs por conta própria. Você pode executar funções duráveis em um Azure WebJob ou em qualquer lugar que um aplicativo de console é executado.

O exemplo de funções duráveis de encadeamento de propriedades está disponível em uma versão do SDK do WebJobs: realize download ou clonar as funções do [repositório durável](https://github.com/azure/azure-functions-durable-extension/) e navegue até a *pasta de exemplos \\webjobssdk\\oencadeamento*.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com os conceitos básicos do SDK do WebJobs, desenvolvimento de bibliotecas de classe C# para funções do Azure Functions e as funções duráveis. Se você precisar obter uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar sua primeira função usando o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções duráveis](durable-functions-sequence.md)

Para completar as etapas neste artigo:

* [Instale o Visual Studio 2017 versão 15.6 ou posterior](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure**.

  Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas > Obter Ferramentas e Recursos**.

  (Você pode usar o [código do Visual Studio](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio Code.)

* Instale e execute o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) 5.2 ou posterior. Uma alternativa é atualizar o arquivo *App. config* com uma cadeia de caracteres de conexão de armazenamento do Microsoft Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Este artigo explica como desenvolver um projeto de 2. x do SDK do WebJobs (equivalente a versão do Azure Functions 1. x). Para obter informações sobre versão 3. x, consulte [SDK do WebJobs 3. x](#webjobs-sdk-3x) posteriormente neste artigo.

## <a name="create-console-app"></a>Criar um aplicativo de console

Um projeto do SDK do WebJobs é apenas um projeto de aplicativo de console com os pacotes do NuGet apropriados instalados.

Na caixa de diálogo do Visual Studio **Novo Projeto**, selecione **Área de trabalho clássica do Windows > Aplicativo de Console (.NET Framework)**. No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

O Visual Studio também tem um modelo de projeto do WebJob, você pode usar selecionando **Nuvem > Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Você precisará dos pacotes do NuGet para o SDK do WebJobs, associações de núcleo, a estrutura do registro em log e a extensão de tarefa durável. Aqui estão comandos do **Console Gerenciador de Pacotes** comandos para esses pacotes, com números de versão estável mais recente a partir da data em que este artigo foi gravado:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Você também precisa de provedores de log. Os comandos a seguir instalam o provedor do Application Insights e `ConfigurationManager`. O `ConfigurationManager` permite que você obtenha a chave de instrumentação do Application Insights de configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Para usar a extensão de Funções Duráveis, chame `UseDurableTask` no `JobHostConfiguration` do objeto em seu `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no `DurableTaskExtension` de objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método também é o local para configurar os provedores de log. O exemplo a seguir configura o console e provedores do Application Insights.

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

Há algumas diferenças entre o código que você grava para funções do SDK do WebJobs em comparação o que você grava para o serviço de Azure Functions.

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

Em um projeto do SDK do WebJobs, você pode chamar métodos no objeto de cliente de orquestração em vez de enviar solicitações HTTP. Os métodos a seguir correspondem a três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função de cliente de orquestração do projeto de exemplo inicia a função do orquestrador e, em seguida, entra em um loop que chama `GetStatusAsync` cada 2 segundos:

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

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK do WebJobs localmente e implantá-lo em um Webjob do Azure, consulte [começar com o SDK do WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o Emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. Se você quiser ver logs no Application Insights ao executar localmente:

     a. Crie um recurso Application Insights, tipo de aplicativo **Geral**.

    b. Salvar a chave de instrumentação no arquivo *App.config*.

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Criar um aplicativo Web e uma conta de armazenamento.

1. No aplicativo da web, salve a cadeia de caracteres de conexão de armazenamento em uma configuração de aplicativo chamada AzureWebJobsStorage.

1. Crie um recurso Application Insights, tipo de aplicativo **Geral**.

1. Salve a chave de instrumentação em uma configuração de aplicativo chamada APPINSIGHTS_INSTRUMENTATIONKEY.

1. Implantar como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

A principal mudança introduzida por 3.x é o uso do .NET Core em vez do .NET Framework. Para criar um projeto de 3. x, as instruções são as mesmas, com as seguintes exceções:

1. Criar um aplicativo de console do aplicativo .NET Core. No Visual Studio, caixa de diálogo **Novo Projeto**, selecione **.NET Core > Aplicativo de Console (.NET Core)**. O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.0`.

1. Escolha a versão de pré-lançamento 3. x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Altere `Main` código do método para obter a cadeia de caracteres de conexão de armazenamento e a chave de instrumentação do Application Insights em um arquivo *appsettings. JSON*, usando a estrutura de configuração do .NET Core.  Aqui está um exemplo:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString =
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SDK do WebJobs, consulte [como usar o SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md).