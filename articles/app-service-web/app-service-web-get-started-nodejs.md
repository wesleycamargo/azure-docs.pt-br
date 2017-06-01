---
title: Criar um Aplicativo do Node.js no Aplicativo Web do Azure | Microsoft Docs
description: "Implante seu primeiro Node.js Hello World no Aplicativo do Serviço de Aplicativo em minutos."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/05/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ced6f54603120d8832ee417b02b6673f80a99613
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Criar um Aplicativo Node.js no aplicativo Web

Este tutorial de início rápido orienta como desenvolver e implantar um aplicativo Node.js no Azure. Executaremos o aplicativo usando um [Plano do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) e criaremos e configuraremos um novo Aplicativo Web nele usando a CLI do Azure. Em seguida, usaremos o git para implantar nosso aplicativo Node.js no Azure.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux. Deve levar apenas cerca de cinco minutos para concluir todas as etapas abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar essa amostra, baixe e instale o seguinte:

* [Git](https://git-scm.com/)
* [ Node.js e NPM](https://nodejs.org/)
* [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Clone o repositório de aplicativos de exemplo Hello World em seu computador local.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Altere para o diretório que contém o código de exemplo.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo localmente abrindo uma janela do terminal e usando o script `npm start` do exemplo para iniciar o servidor http interno do Node.js.

```bash
npm start
```

Abra um navegador da Web e navegue até o exemplo.

```bash
http://localhost:1337
```

Você poderá ver a mensagem **Hello World** no aplicativo de exemplo exibido na página.

![localhost-hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Agora, usaremos a CLI 2.0 do Azure em uma janela do terminal para criar os recursos necessários para hospedar nosso aplicativo Node.js no Azure. Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure, como os aplicativos Web, bancos de dados e contas de armazenamento, são implantados e gerenciados.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

Crie um [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO" com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo a seguir cria um Plano do Serviço de Aplicativo denominado `quickStartPlan` usando o tipo de preço **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que um plano do Serviço de Aplicativo foi criado, crie um [aplicativo Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) no plano do Serviço de Aplicativo `quickStartPlan`. O aplicativo Web nos dá um espaço de hospedagem para implantar nosso código, bem como fornece uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o Aplicativo Web.

No comando abaixo, substitua o espaço reservado `<app_name>` por seu próprio nome exclusivo de aplicativo. O `<app_name>` é usado no site do DNS padrão para o aplicativo Web. Se `<app_name>` for não exclusivo, você obterá a mensagem de erro amigável "O site com o nome <app_name> fornecido já existe".

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Navegue até o site para ver seu Aplicativo Web recém-criado.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

Agora, criamos um novo Aplicativo Web vazio no Azure.

## <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

Você pode implantar seu Aplicativo Web de várias maneiras, incluindo o FTP, Git local, bem como o GitHub, Visual Studio Team Services e Bitbucket.

Use o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso do git local para o Aplicativo Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Copie a saída do terminal, pois ela será usada na próxima etapa.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Adicione um Azure remoto ao seu repositório Git local.

```bash
git remote add azure <paste-previous-command-output-here>
```

Envie por push para o Azure remoto para implantar seu aplicativo. A senha que você forneceu anteriormente ao criar o usuário de implantação é solicitada a você. Verifique se você inseriu a senha que você criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

Durante a implantação, o Serviço de Aplicativo do Azure comunicará seu andamento ao Git.

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

Neste momento, a página que exibe a mensagem Hello World está em execução usando nosso código Node.js executado como um aplicativo Web do Serviço de Aplicativo do Azure.

## <a name="updating-and-deploying-the-code"></a>Atualizando e Implantando o Código

Usando um editor de texto local, abra o arquivo `index.js` no aplicativo do Node.js e faça uma pequena alteração no texto dentro da chamada para `response.end`:

```nodejs
response.end("Hello Azure!");
```

Confirme suas alterações no git, em seguida, envie as alterações do código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois que a implantação for concluída, mude de volta para a janela do navegador aberta na etapa **Navegar até o aplicativo** e clique em Atualizar.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gerenciar seu novo aplicativo Web do Azure

Vá para o portal do Azure para examinar o aplicativo Web que você acabou de criar.

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Você foi para a _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente).

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

![Folha Serviço de Aplicativo no portal do Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário

**Parabéns!** Você implantou seu primeiro aplicativo do Node.js no Serviço de Aplicativo.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [Explore os scripts da CLI dos aplicativos Web de exemplo](app-service-cli-samples.md)

