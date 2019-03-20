---
title: Integrar com um pipeline de integração e entrega contínua usando a configuração de aplicativo do Azure | Microsoft Docs
description: Saiba como gerar um arquivo de configuração usando os dados na configuração de aplicativo do Azure durante a integração contínua e entrega
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224321"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline de CI/CD

Você pode melhorar a resiliência do seu aplicativo contra a possibilidade remota de não ser capaz de alcançar a configuração de aplicativo do Azure. Para fazer isso, empacote os dados de configuração atual em um arquivo que é implantado com o aplicativo e carregados localmente durante sua inicialização. Essa abordagem garante que seu aplicativo tenha pelo menos valores de configuração padrão. Esses valores são substituídos por alterações mais recentes em um repositório de configuração do aplicativo quando ele estiver disponível.

Usando o [exportar](./howto-import-export-data.md#export-data) função da configuração de aplicativo do Azure, você pode automatizar o processo de recuperação de dados de configuração atual como um único arquivo. Em seguida, inserir esse arquivo em uma etapa de compilação ou implantação em seu pipeline de implantação contínua (CI/CD) e integração contínua.

O exemplo a seguir mostra como incluir a configuração de aplicativo de dados como uma compilação de etapa para o aplicativo web introduzido em guias de início rápido. Antes de continuar, conclua [criar um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste guia de início rápido. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Se você criar localmente, baixe e instale o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você ainda não fez isso.

Para fazer uma compilação de nuvem, com DevOps do Azure, por exemplo, verifique se o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) é instalado em seu sistema de compilação.

## <a name="export-an-app-configuration-store"></a>Exportar um repositório de configuração de aplicativo

1. Abra seu *. csproj* de arquivo e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Adicione a *ConnectionString* associado com seu repositório de configurações do aplicativo como uma variável de ambiente.

2. Abra Program.cs e atualize o `CreateWebHostBuilder` método para usar o arquivo exportado do JSON, chamando o `config.AddJsonFile()` método.

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

1. Definir uma variável de ambiente denominada **ConnectionString**e defina-a como a chave de acesso ao seu armazenamento de configuração do aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para compilar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

3. Após a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo web localmente:

        dotnet run

4. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão para o aplicativo web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Próximas etapas

* [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
