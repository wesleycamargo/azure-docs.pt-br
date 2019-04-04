---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Framework | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b4cb571653cbe69939a1cbdc92338663e4e7125f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576065"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. É possível utilizá-lo para armazenar e gerenciar facilmente todas as configurações de aplicativo em um local separado do código. Este Início Rápido mostra como incorporar o serviço em um aplicativo de console da área de trabalho do Windows baseado no .NET Framework.

![Início rápido local completo](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este início rápido, instale o [Visual Studio 2017](https://visualstudio.microsoft.com/vs) e o [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) ou posterior, caso ainda não tenha feito isso.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console do .NET

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

2. Em **Novo Projeto**, selecione **Instalado** > **Visual C#** > **Windows Desktop**. Selecione **Aplicativo do Console (.NET Framework)** e insira um nome para o projeto. Selecione **.NET Framework 4.7.1** ou acima e selecione**OK**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de configurações de aplicativo

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto. Se você não conseguir localizá-los, marque a caixa de seleção **	Incluir pré-lançamento**.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Atualize o arquivo *App.config* do projeto, conforme a seguir:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   A cadeia de conexão do repositório de configurações de aplicativo é lida a partir da variável de ambiente `ConnectionString`. Adicione o `Environment` construtor de configuração antes de `MyConfigStore` na propriedade `configBuilders` da seção `appSettings`.

3. Abra *Program.cs* e atualize o método `Main` para usar a Configuração de Aplicativo, chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** como a cadeia de conexão do repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Reinicie o Visual Studio para permitir que a alteração tenha efeito. Pressione Ctrl + F5 para criar e executar o aplicativo do console.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo repositório de configurações de aplicativo e o utilizou com um aplicativo do console do .NET Framework. Para saber mais sobre como usar a Configuração de Aplicativo, consulte o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
