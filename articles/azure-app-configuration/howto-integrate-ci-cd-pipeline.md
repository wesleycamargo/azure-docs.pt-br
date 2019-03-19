---
title: Integrar com um pipeline de integração e entrega contínuo usando a configuração de aplicativo do Azure | Microsoft Docs
description: Saiba como gerar um arquivo de configuração usando dados de configuração de aplicativo do Azure durante a integração contínua e entrega
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957361"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline de CI/CD

Para aumentar a resiliência do seu aplicativo contra a possibilidade remota de não ser capaz de alcançar a configuração de aplicativo do Azure, você deve empacotar os dados de configuração atual em um arquivo que é implantado com o aplicativo e carregados localmente durante sua inicialização . Essa abordagem garante que seu aplicativo será ter pelo menos os valores de configuração padrão. Esses valores serão substituídos por alterações mais recentes em um repositório de configuração do aplicativo quando ele estiver disponível.

Usando o [ **exportar** ](./howto-import-export-data.md#export-data) função da configuração de aplicativo do Azure, você pode automatizar o processo de recuperação de dados de configuração atual como um único arquivo. Em seguida, você pode inserir esse arquivo em uma etapa de compilação ou implantação em seu pipeline de implantação contínua e integração contínua.

O exemplo a seguir mostra como incluir a configuração de aplicativo de dados como uma compilação de etapa para o aplicativo web introduzido em guias de início rápido. Conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro antes de continuar.

Você pode usar qualquer editor de código para concluir as etapas deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Se você criar localmente, baixe e instale [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você ainda não fez isso.

Se você quiser fazer um build de nuvem, com DevOps do Azure, por exemplo, certifique-se [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) é instalado em seu sistema de compilação.

## <a name="export-app-configuration-store"></a>Exportar o armazenamento de configuração de aplicativo

1. Abra seu *. csproj* de arquivo e adicione o seguinte:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    O *ConnectionString* associados com a sua configuração de aplicativo store deve ser adicionada como uma variável de ambiente.

2. Abra *Program.cs* e atualize o `CreateWebHostBuilder` método para usar o arquivo json exportado, chamando o `config.AddJsonFile()` método.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de configurações de aplicativo. Caso esteja usando o Prompt de Comando do Windows, execute o seguinte comando e reinicie o Prompt de Comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Caso esteja usando o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Caso esteja usando o macOS ou o Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para compilar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

3. Após a compilação, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

4. Inicie uma janela do navegador e navegue para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Próximas etapas

* [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
