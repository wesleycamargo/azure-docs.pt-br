---
title: Tutorial para o uso da configuração dinâmica da Configuração de Aplicativo do Azure em um aplicativo ASP.NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cf872766a18c5691f6c094d71a0c29f6bcf736da
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579029"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo ASP.NET Core

O ASP.NET Core tem um sistema de configuração conectável que pode ler dados de configuração de uma variedade de fontes. Ele pode lidar com alterações em tempo real sem fazer com que um aplicativo reinicie. ASP.NET Core dá suporte a associação de parâmetros de configuração para classes .NET fortemente tipadas. Isso injeta-lo em seu código usando diversos `IOptions<T>` padrões. Um desses padrões, especificamente `IOptionsSnapshot<T>`, fornece o recarregamento automático da configuração do aplicativo quando os dados subjacentes são alterados. 

Você pode injetar `IOptionsSnapshot<T>` nos controladores do aplicativo para acessar a configuração mais recente armazenada na Configuração de Aplicativo do Azure. Você também pode configurar a biblioteca de clientes ASP.NET Core da Configuração de Aplicativo para monitorar continuamente e recuperar qualquer alteração em um repositório de configurações de aplicativo. Você define o intervalo periódico para a sondagem.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste início rápido. [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure seu aplicativo para atualizar a configuração em resposta a alterações em um repositório de configurações de aplicativo.
> * Injetar a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados da Configuração de Aplicativo

1. Abra Program.cs e atualize o `CreateWebHostBuilder` método adicionando o método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    O segundo parâmetro no método `.Watch` é o intervalo de sondagem no qual a biblioteca de clientes ASP.NET consulta um repositório de configurações de aplicativo. A biblioteca de cliente verifica a configuração de configuração específica para ver se houve alterações.

2. Adicione um arquivo Settings.cs que define e implementa uma nova classe `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Abra Startup.cs e atualize o método `ConfigureServices` para associar os dados de configuração à classe `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>Usar os dados de configuração mais recentes

1. Abra HomeController.cs no diretório Controllers. Atualize a classe `HomeController` para receber `Settings` por meio de injeção de dependência e faça uso de seus valores.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

2. Abra Index.cshtml no diretório Exibições > Página Inicial e substitua o conteúdo pelo script a seguir:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

2. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Entre no [Portal do Azure](https://aka.ms/azconfig/portal). Selecione **Todos os recursos** e selecione a instância do repositório de configurações do aplicativo que você criou no início rápido.

5. Selecione **Gerenciador de Pares Chave-Valor** e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dados da Configuração de Aplicativo do Azure – agora com atualizações dinâmicas! |

6. Atualize a página do navegador para ver as novas definições de configuração.

    ![Atualização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou uma identidade de serviço gerenciada do Azure para simplificar o acesso à Configuração de Aplicativo e melhorar o gerenciamento de credenciais de seu aplicativo. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [Exemplos de CLI](./cli-samples.md)
