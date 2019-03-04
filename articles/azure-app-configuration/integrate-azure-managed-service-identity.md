---
title: Tutorial para integração às Identidades Gerenciadas do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as Identidades Gerenciadas do Azure para se autenticar na Configuração de Aplicativo do Azure e obter acesso a ela
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
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957344"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Tutorial: Integração às Identidades Gerenciadas do Azure

As [Identidades Gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do Azure Active Directory ajudam a simplificar o gerenciamento de segredos para seu aplicativo de nuvem. Com uma identidade gerenciada, você pode configurar o código para usar a entidade de serviço que foi criada para o serviço de computação do Azure no qual ela é executada, em vez de uma credencial separada armazenada no Azure Key Vault ou uma cadeia de conexão local. A Configuração de Aplicativo do Azure e suas bibliotecas de clientes do .NET Core, do .NET e do Java Spring vêm com suporte interno ao MSI. Embora você não precise usá-lo, o MSI elimina a necessidade de um token de acesso que contenha segredos. O código precisa apenas conhecer o ponto de extremidade de serviço para um repositório de configurações de aplicativo para acessá-lo e você pode inserir esta URL no código diretamente sem a preocupação de expor nenhum segredo.

Este tutorial mostra como você pode aproveitar o MSI para acessar a Configuração de Aplicativo. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro antes de continuar.

Você pode usar qualquer editor de códigos para concluir as etapas deste tutorial. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Permitir acesso a uma identidade gerenciada à Configuração de Aplicativo
> * Configure seu aplicativo para usar uma identidade gerenciada ao conectar-se à Configuração de Aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* [SDK do .NET Core](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerenciada

Para configurar uma identidade gerenciada no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie um aplicativo no [portal do Azure](https://aka.ms/azconfig/portal), como você faria normalmente. Navegue até ele no portal.

2. Role a página para baixo até o grupo **Configurações** no painel de navegação à esquerda e selecione **Identidade**.

3. Na guia **Sistema atribuído**, alterne o **Status** para **Ativado** e clique em **Salvar**.

    ![Definir a identidade gerenciada no Serviço de Aplicativo](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Permitir acesso à Configuração de Aplicativo

1. No [portal do Azure](https://aka.ms/azconfig/portal), clique em **Todos os recursos** e o repositório de configurações de aplicativo criado no Início Rápido.

2. Selecione **IAM (Controle de acesso)**.

3. Na guia **Verificar acesso**, clique em **Adicionar** na interface do usuário de cartão **Adicionar uma atribuição de função**.

4. Defina **Função** como *Colaborador* e **Atribuir acesso a** como *Serviço de Aplicativo* (em *Identidade gerenciada atribuída ao sistema*).

5. Defina **Assinatura** como a sua assinatura do Azure e selecione o recurso do Serviço de Aplicativo para o aplicativo.

6. Clique em **Salvar**.

    ![Adicionar identidade gerenciada](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Usar uma identidade gerenciada

1. Abra *appsettings.json*, adicione o seguinte e substitua *<ponto_de_extremidade_de_serviço>* (incluindo os colchetes) pela URL para o repositório de configurações de aplicativo:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Abra *Program.cs* e atualize o método `CreateWebHostBuilder` substituindo o método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implantar a partir do Git local

A maneira mais fácil de habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu é usar o Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Habilitar Git local com Kudu

Para habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu, execute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar um aplicativo habilitado para Git em vez disso, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o parâmetro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O comando `az webapp create` deve oferecer algo semelhante ao seguinte resultado:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Implante o seu projeto

De volta na _janela do terminal local_, adicione um remoto do Azure ao repositório Git local. Substitua a _\<url>_ pela URL do Git remoto que você obteve de [Habilitar o Git para o seu aplicativo](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando uma senha for solicitada, verifique se você inseriu a senha que criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para o Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Procure o aplicativo Web usando um navegador para verificar se o conteúdo foi implantado.

```bash
http://<app_name>.azurewebsites.net
```

![aplicativo em execução no Serviço de Aplicativo](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Usar a identidade gerenciada em outras linguagens

Os provedores da Configuração de Aplicativo para o .NET Framework e o Java Spring também têm suporte interno para a identidade gerenciada. Nesses casos, basta usar o ponto de extremidade de URL do repositório de configurações de aplicativo em vez da cadeia de conexão completa ao configurar um provedor. Por exemplo, para o aplicativo de console .NET Framework criado no Início Rápido, especifique as seguintes configurações no arquivo *App.config*:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou uma identidade de serviço gerenciada do Azure para simplificar o acesso à Configuração de Aplicativo e melhorar o gerenciamento de credenciais de seu aplicativo. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras da CLI](./cli-samples.md)
