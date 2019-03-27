---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Core | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5501e92b9a9d977f74bf4ed028b3cd3de4e56133
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225375"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Core com a Configuração de Aplicativo

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. É possível utilizá-lo para armazenar e gerenciar facilmente todas as configurações de aplicativo em um local separado do código. Este Início Rápido mostra como incorporar o serviço em um aplicativo de console .NET Core.

Você pode usar qualquer editor de código para executar as etapas deste início rápido. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console .NET Core

Você usará a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um projeto de aplicativo de console .NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma nova pasta para o seu projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web do MVC do ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de configurações de aplicativo

1. Adicione uma referência ao pacote NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, executando o seguinte comando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Execute o seguinte comando para restaurar pacotes do projeto:

        dotnet restore

3. Abra o *Program.cs* e atualize o método `Main` para usar a Configuração de Aplicativo chamando o método `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usa o Windows PowerShell, execute o comando a seguir:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usa macOS ou Linux, execute o comando a seguir:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Execute o seguinte comando para compilar o aplicativo de console:

        dotnet build

3. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

        dotnet run

    ![Execução do aplicativo do Início Rápido](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você criou um repositório de configurações de aplicativo e usou-o com um aplicativo de console .NET Core. Para saber mais sobre como usar a Configuração de Aplicativo, vá para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
