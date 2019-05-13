---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar os serviços em funções do .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 1ef688dff65dc11f875f76e2f9127bf89af2f2b9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074584"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

O Azure Functions dá suporte ao padrão de projeto de software de injeção de dependência (DI), que é uma técnica para conseguir [inversão de controle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

O Azure Functions se baseia nos recursos de injeção de dependência do ASP.NET Core.  Você deve compreender os serviços, tempos de vida e padrões de projeto de [injeção de dependência do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) antes de usá-las em funções.

## <a name="installing-dependency-injection-packages"></a>Instalar os pacotes de injeção de dependência

Para usar os recursos de injeção de dependência, você precisará incluir o pacote do NuGet que expõe essas APIs.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Registrando serviços

Para registrar os serviços, você pode criar um método `Configure` e adicionar componentes a uma instância `IFunctionsHostBuilder`. O host do Azure Functions cria um `IFunctionsHostBuilder` e o passa diretamente no seu método configurado.

Para registrar o seu método `Configure`, você deve adicionar um atributo de assembly que especifica o tipo para seu método `Configure` usando o atributo `FunctionsStartup`.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Os aplicativos de função do Azure fornecem os mesmos tempos de vida do serviço como [injeção de dependência do ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), temporário, escopo, singleton e.

Em um aplicativo de função, um tempo de vida de serviço com escopo corresponde a um tempo de vida de execução de função. Os serviços com escopo são criados uma vez por execução. As solicitações posteriores para o serviço durante a execução vão reutilizar essa instância. Um tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância.

Serviços de tempo de vida singleton são recomendados para conexões de clientes e, por exemplo uma `SqlConnection`, `CloudBlobClient`, ou `HttpClient`.

Exibir ou baixar uma [exemplo de tempos de vida de serviço diferentes](http://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Serviços de registro em log

Se você precisar de seu próprio provedor de registro em log, a maneira recomendada é registrar um `ILoggerProvider`.  Para o Application Insights, o Functions adicionará Application Insights automaticamente para você.  

> [!WARNING]
> Não adicione `AddApplicationInsightsTelemetry()` aos serviços de coleção pois ela registrará os serviços que entrarão em conflito com o que é fornecida pelo ambiente. 
 
## <a name="function-app-provided-services"></a>Serviços de aplicativo fornecida da função

O host de função registra muitos serviços em si. Abaixo estão os serviços nos quais é seguro assumir uma dependência. Não há suporte para registrar ou depender de outros serviços de host. Se houver outros serviços em que você deseja assumir uma dependência, por favor [crie um problema e uma discussão no GitHub](https://github.com/azure/azure-functions-host).

|Tipo de serviço|Tempo de vida|DESCRIÇÃO|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Responsável por fornecer a ID da instância do host|

### <a name="overriding-host-services"></a>Substituindo serviços de Host

Substituir serviços fornecidos pelo host não é suportado atualmente.  Se houver serviços que você deseja substituir, por favor [criar um problema e uma discussão no GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Como monitorar seu aplicativo de funções](functions-monitoring.md)
* [Práticas recomendadas para funções](functions-best-practices.md)
