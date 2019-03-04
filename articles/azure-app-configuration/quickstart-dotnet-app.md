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
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962155"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. Ela permite armazenar e gerenciar com facilidade todas as suas configurações de aplicativo em um só lugar, separado do código. Este Início Rápido mostra como incorporar o serviço em um aplicativo de console da área de trabalho do Windows baseado no .NET Framework.

![Início Rápido completo local](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este Início Rápido, instale o [Visual Studio 2017](https://visualstudio.microsoft.com/vs) e o [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) ou posterior, caso ainda não tenha feito isso.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console do .NET

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

2. Na caixa de diálogo **Novo Projeto**, selecione **Instalado**, expanda **Visual C#** > **Área de Trabalho do Windows**, selecione **Aplicativo do Console (.NET Framework)**, digite um **Nome** para o projeto, escolha **.NET Framework 4.7.1** ou posterior e clique em **OK**.

## <a name="connect-to-app-configuration-store"></a>Conectar-se ao repositório de configurações de aplicativo

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet…**. Na guia **Procurar**, pesquise e adicione os pacotes NuGet a seguir ao projeto (marque a caixa **Incluir pré-lançamento** se não encontrá-los).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Atualize o arquivo *App.config* do projeto da seguinte maneira:

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

   Observe que, como leremos a cadeia de conexão do repositório de configurações de aplicativo na variável de ambiente `ConnectionString`, será importante adicionar o construtor de configuração `Environment` antes do `MyConfigStore` na propriedade `configBuilders` da seção `appSettings`.

3. Abra *Program.cs* e atualize o método `Main` para usar a Configuração de Aplicativo chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** como a cadeia de conexão do repositório de configurações de aplicativo. Caso esteja usando o Prompt de Comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Caso esteja usando o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Reinicie o Visual Studio para permitir que a alteração entre em vigor e, em seguida, pressione **Ctrl+F5** no teclado para compilar e executar o aplicativo de console.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você criou um repositório de configurações de aplicativo e usou-o com um aplicativo de console .NET Framework. Para saber mais sobre como usar a Configuração de Aplicativo, continue no próximo tutorial, que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
