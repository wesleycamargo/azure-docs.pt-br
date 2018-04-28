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
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 842cd6f67a04bec0ed06282bdeeea8b8a51c0667
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação do Git local para o Serviço de Aplicativo do Azure

Este guia de instruções mostra como implantar seu código para o [Serviço de Aplicativo do Azure](app-service-web-overview.md) de um repositório do Git no computador local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Para seguir as etapas neste guia de instruções:

* [Instalar o Git](http://www.git-scm.com/downloads).
* Mantenha um repositório Git local com o código que deseja implantar.

Para usar um repositório de amostra para acompanhar, execute o seguinte comando na janela do terminal local:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Preparar o repositório

Certifique-se de que a raiz do repositório tenha os arquivos corretos em seu projeto.

| Tempo de execução | Arquivos do diretório raiz |
|-|-|
| ASP.NET (somente Windows) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (somente Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ com um script de início |
| Python (somente Windows) | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Trabalhos Web | _\<nome_trabalho>/run.\<extension>_ em _App\_Data/jobs/continuous_ (para WebJobs contínuos) ou _App\_Data/jobs/triggered_ (para WebJobs disparados). Para saber mais, veja a [documentação de WebJobs Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funções | Confira [Implantação contínua para Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar a implantação, você pode incluir um arquivo _.deployment_ na raiz do repositório. Para saber mais, confira [Personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Certifique-se de `git commit` todas as alterações que deseja implantar.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Habilitar o Git para o seu aplicativo

Para habilitar a implantação do Git para um aplicativo do Serviço de Aplicativo existente, execute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) no Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar um aplicativo habilitado para Git em vez disso, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) no Cloud Shell com o parâmetro `--deployment-local-git`.

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

## <a name="deploy-your-project"></a>Implante o seu projeto

De volta na _janela do terminal local_, adicione um remoto do Azure ao repositório Git local. Substitua a _\<url>_ pela URL do Git remoto que você obteve de [Habilitar o Git para o seu aplicativo](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando uma senha for solicitada, verifique se você inseriu a senha que criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para o Python. 

Depois que a implantação for concluída, o seu aplicativo no Portal do Azure agora deve ter um registro de seu `git push` na página **Opções de Implantação**.

![](./media/app-service-deploy-local-git/deployment_history.png)

Navegue até seu aplicativo para verificar se o conteúdo foi implantado.

## <a name="troubleshooting"></a>solução de problemas

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
