---
title: Tutorial para integração às identidades gerenciadas do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as identidades gerenciadas do Azure para se autenticar na Configuração do Aplicativo Azure AD e obter acesso a ele
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
ms.openlocfilehash: 6e161bcf2c38db0fd614eac1a211218785179ec3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620762"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Tutorial: Integração às Identidades Gerenciadas do Azure

As [Identidades Gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do Azure Active Directory ajudam a simplificar o gerenciamento de segredos para seu aplicativo de nuvem. Com uma identidade gerenciada, você pode configurar seu código para usar a entidade de serviço que foi criada para o serviço de computação do Azure, que ele é executado. Você usa uma identidade gerenciada, em vez de uma credencial separada armazenada no Azure Key Vault ou uma cadeia de caracteres de conexão local. 

A Configuração do Aplicativo Azure AD e suas bibliotecas de clientes do .NET Core, do .NET e do Java Spring vêm com suporte interno à identidade do serviço gerenciado (MSI) integrado. Embora você não precise usá-lo, o MSI elimina a necessidade de um token de acesso que contenha segredos. Seu código precisa saber apenas o ponto de extremidade de serviço para uma configuração de aplicativo armazenar para acessá-la. Você pode inserir esta URL no seu código diretamente sem a preocupação de expor nenhum segredo.

Este tutorial mostra como você pode aproveitar o MSI para acessar a Configuração de Aplicativo. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Permita acesso a uma identidade gerenciada à Configuração de Aplicativo.
> * Configure seu aplicativo para usar uma identidade gerenciada ao conectar-se à Configuração de Aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter:

* [SDK do .Net Core](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerenciada

Para configurar uma identidade gerenciada no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie um aplicativo no [portal do Azure](https://aka.ms/azconfig/portal), como você faria normalmente. Vá até ele no portal.

2. Role a página para baixo até o grupo **Configurações** no painel de navegação à esquerda e selecione **Identidade**.

3. Na guia **Sistema atribuído**, alterne o **Status** para **Ativado** e selecione **Salvar**.

    ![Definir a identidade gerenciada no Serviço de Aplicativo](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Permitir acesso à Configuração de Aplicativo

1. No [portal do Azure](https://aka.ms/azconfig/portal), selecione **Todos os recursos** e selecione o repositório de configurações que você criou no início rápido.

2. Selecione **IAM (Controle de acesso)**.

3. Na guia **Verificar acesso**, selecione **Adicionar** na interface do usuário de cartão **Adicionar uma atribuição de função**.

4. Em **Função**, selecione **Colaborador**. Em **Atribuir acesso**, selecione **Serviço de Aplicativo** em **Identidade gerenciada atribuída ao sistema**.

5. Em **Assinatura**, selecione sua assinatura do Azure. Selecione o recurso de Serviço de Aplicativo para o seu aplicativo.

6. Clique em **Salvar**.

    ![Adicionar uma identidade gerenciada](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Usar uma identidade gerenciada

1. Abra *appsettings.json* e adicione o seguinte script. Substitua *<service_endpoint>*, incluindo os colchetes, com a URL para o seu repositório de configurações do aplicativo:

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
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implantar a partir do Git local

A maneira mais fácil de habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu para usar o Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Habilitar Git local com Kudu

Para habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu, execute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar um aplicativo habilitado para Git em vez disso, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o `--deployment-local-git`parâmetro.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O comando `az webapp create` oferece algo semelhante para a saída a seguir:

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

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando for solicitada uma senha, insira a senha que você criou em [Configurar um usuário de implantação](#configure-a-deployment-user). Não use a senha que você usa para entrar no portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para o Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Procure o aplicativo web usando um navegador para verificar se o conteúdo foi implantado.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicativo em execução no Serviço de Aplicativo](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Usar a identidade gerenciada em outras linguagens

Os provedores da Configuração de Aplicativo para o .NET Framework e o Java Spring também têm suporte interno para a identidade gerenciada. Nesses casos, use o ponto de extremidade de URL do repositório de configurações de aplicativo em vez da cadeia de conexão completa ao configurar um provedor. Por exemplo, para o aplicativo de console .NET Framework criado no Início Rápido, especifique as seguintes configurações no arquivo *App.config*:

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
> [Exemplos de CLI](./cli-samples.md)
