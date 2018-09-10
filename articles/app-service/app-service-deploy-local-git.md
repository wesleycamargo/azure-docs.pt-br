---
title: Implantação do Git local para o Serviço de Aplicativo do Azure
description: Saiba como habilitar a implantação do Git local no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: f461a9a7cc900ce5f8fdba7b255417b1790d3f4d
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146293"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação do Git local para o Serviço de Aplicativo do Azure

Este guia de instruções mostra como implantar seu código para o [Serviço de Aplicativo do Azure](app-service-web-overview.md) de um repositório do Git no computador local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções:

* [Instalar o Git](http://www.git-scm.com/downloads).
* Mantenha um repositório Git local com o código que deseja implantar.

Para usar um repositório de amostra para acompanhar, execute o seguinte comando na janela do terminal local:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Implantar do Git local com builds Kudu

A maneira mais fácil de habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu é usar o Cloud Shell.

### <a name="create-a-deployment-user"></a>Crie um usuário de implantação

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

De volta na _janela do terminal local_, adicione um remoto do Azure ao repositório Git local. Substitua a _\<url>_ pela URL do Git remoto que você obteve de [Habilitar o Git para o seu aplicativo](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando uma senha for solicitada, verifique se você inseriu a senha que criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para o Python. 

Navegue até seu aplicativo para verificar se o conteúdo foi implantado.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Implantar do Git local com builds VSTS

> [!NOTE]
> Para o Serviço de Aplicativo criar as definições de versão e de build necessárias em sua conta do VSTS, sua conta do Azure deve ter a função de **Proprietário** em sua assinatura do Azure.
>

Para habilitar a implantação do Git local no seu aplicativo com o servidor de build do Kudu, navegue até o aplicativo no [portal do Azure](https://portal.azure.com).

No painel de navegação esquerdo da página do aplicativo, clique em **Centro de Implantação** > **Git Local** > **Continuar**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Clique em **Entrega Contínua do VSTS** > **Continuar**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

Na página **Configurar**, configure uma nova conta do VSTS ou especifique uma conta existente. Ao terminar, clique em **Continuar**.

> [!NOTE]
> Se deseja usar uma conta do VSTS existente que não esteja listada, [vincule a conta do VSTS à sua assinatura do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Na página **Teste**, escolha se deseja habilitar testes de carga. Em seguida, clique em **Continuar**.

Dependendo do [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do Serviço de Aplicativo, talvez você possa ver uma página **Implantar no preparo**. Escolha se deseja habilitar slots de implantação e clique em **Continuar**.

Na página **Resumo**, verifique as opções e clique em **Concluir**.

Pode levar alguns minutos para que a conta do VSTS esteja pronta. Quando estiver pronta, copie a URL do repositório Git no centro de implantação.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

De volta na _janela do terminal local_, adicione um remoto do Azure ao repositório Git local. Substitua _\<url>_ pela URL da última etapa.

```bash
git remote add vsts <url>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando solicitado pelo Gerenciador de Credenciais do Git, entre com seu usuário do visualstudio.com. Para métodos de autenticação adicionais, consulte [Visão geral da autenticação do VSTS](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Depois que a implantação for concluída, você poderá encontrar o progresso do build em `https://<vsts_account>.visualstudio.com/<project_name>/_build` e o progresso da implantação em `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Navegue até seu aplicativo para verificar se o conteúdo foi implantado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Solução de problemas de implantação do Kudu

Estes são erros ou problemas comuns ao usar o Git para publicar em um aplicativo do Serviço de Aplicativo no Azure:

---
**Sintoma**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Causa**: esse erro poderá ocorrer se o aplicativo não estiver em funcionamento.

**Resolução**: inicie o aplicativo no portal do Azure. A implantação do Git não está disponível quando o Aplicativo Web é interrompido.

---
**Sintoma**: `Couldn't resolve host 'hostname'`

**Causa**: esse erro pode ocorrer se as informações de endereço inseridas ao criar o azure remoto estiverem incorretas.

**Resolução**: Use o comando `git remote -v` para listar todos os remotos, juntamente com a URL associada. Verifique se a URL do remote do 'azure' está correta. Se necessário, remova e recrie esse remoto usando a URL correta.

---
**Sintoma**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Causa**: esse erro pode ocorrer se você não especificar um branch durante `git push` ou se você não tiver configurado o valor `push.default` em `.gitconfig`.

**Resolução**: execute `git push` novamente, especificando o branch mestre. Por exemplo: 

```bash
git push azure master
```

---
**Sintoma**: `src refspec [branchname] does not match any.`

**Causa**: esse erro pode ocorrer se você tentar enviar para um branch que não seja o mestre no remote 'azure'.

**Resolução**: execute `git push` novamente, especificando o branch mestre. Por exemplo: 

```bash
git push azure master
```

---
**Sintoma**: `RPC failed; result=22, HTTP code = 5xx.`

**Causa**: esse erro poderá ocorrer se você tentar enviar por push um repositório Git de grande porte por HTTPS.

**Resolução**: altere a configuração do Git no computador local para aumentar o postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**Sintoma**: `Error - Changes committed to remote repository but your web app not updated.`

**Causa**: esse erro poderá ocorrer se você estiver implantando um aplicativo do Node.js com um arquivo _package.json_ que especifica os módulos adicionais necessários.

**Resolução**: as mensagens adicionais com 'npm ERR!' devem ser registradas antes desse erro e podem fornecer um contexto extra sobre a falha. A seguir estão as causas conhecidas desse erro e a mensagem 'npm ERR!' correspondente: mensagem:

* **Arquivo malformado package.json**: npm ERR! Não foi possível ler as dependências.
* **Um módulo nativo que não tenha uma distribuição binária para o Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OU
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Recursos adicionais

* [Documentação do projeto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Implantação contínua no Serviço de Aplicativo do Azure](app-service-continuous-deployment.md)
* [Exemplo: Criar Aplicativo Web e implantar código de um repositório Git local (CLI do Azure)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Exemplo: Criar Aplicativo Web e implantar código de um repositório Git local (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
