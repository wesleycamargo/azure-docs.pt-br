---
title: Início Rápido da Configuração de Aplicativo do Azure com o ASP.NET Core | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f9d21cb1b047fcc1043ca2d92f718bb5821879a3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226053"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo do Azure

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. É possível utilizá-lo para armazenar e gerenciar facilmente todas as configurações de aplicativo em um local separado do código. Este Início Rápido mostra como incorporar o serviço em um aplicativo Web ASP.NET Core. 

O ASP.NET Core cria um único objeto de configuração baseado no par chave-valor, usando as configurações de uma ou mais fontes de dados especificadas por aplicativo. Essas origens de dados são conhecidas como *provedores de configuração*. Como o cliente .NET Core da Configuração de Aplicativo é implementado como um provedor, o serviço aparece como outra fonte de dados.

Você pode usar qualquer editor de código para executar as etapas deste início rápido. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo Web do MVC do ASP.NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, MacOS e Linux.

1. Crie uma nova pasta para o seu projeto. Para este início rápido, nomeie-o *TestAppConfig*.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web do MVC do ASP.NET Core:

        dotnet new mvc

## <a name="add-secret-manager"></a>Adicionar o Gerenciador de Segredos

Adicione a [ferramenta Gerenciador de Segredos](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao projeto. A ferramenta Gerenciador de Segredos armazena dados confidenciais para o trabalho de desenvolvimento fora da árvore do seu projeto. Essa abordagem ajuda a impedir o compartilhamento acidental de segredos do aplicativo no código-fonte.

- Abra seu arquivo *.csproj*. Adicione um elemento `UserSecretsId`, conforme mostrado aqui e substitua o valor pelo seu próprio, que geralmente é um GUID. Salve o arquivo.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de configurações de aplicativo

1. Adicione uma referência ao pacote NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, executando o seguinte comando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Execute o seguinte comando para restaurar pacotes do projeto:

        dotnet restore

3. Adicione um segredo chamado *ConnectionStrings:AppConfig* ao Gerenciador de Segredos.

    Esse segredo contém a cadeia de conexão para acessar o repositório de configurações de aplicativo. Substitua o valor no seguinte comando pela cadeia de conexão do repositório de configurações de aplicativo.

    Este comando deve ser executado no mesmo diretório que o arquivo *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    O Gerenciador de Segredos é usado apenas para testar o aplicativo Web localmente. Quando o aplicativo for implantado, por exemplo, no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web), você usará uma configuração de aplicativo, por exemplo, **Cadeias de Conexão** no Serviço de Aplicativo. Você usa essa configuração, em vez de armazenar a cadeia de conexão com Gerenciador de Segredos.

    Esse segredo é acessado com a API de configuração. Um sinal de dois pontos (:) funciona no nome da configuração com a API de configuração em todas as plataformas com suporte. Consulte [Configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Abra Program.cs e atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativo, chamando o método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. Abra Index.cshtml no diretório Exibições > Página Inicial e substitua o conteúdo pelo código a seguir:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Abra _Layout.cshtml no diretório Exibições > Compartilhado e substitua o conteúdo pelo código a seguir:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

2. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo repositório de configurações de aplicativo e usou com um aplicativo Web do ASP.NET Core. Para saber mais sobre como usar a Configuração de Aplicativo, vá para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
