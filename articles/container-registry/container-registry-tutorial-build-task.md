---
title: Tutorial - Automatizar builds de imagem de contêiner com o Build de Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá como configurar uma tarefa de build para disparar builds de imagem de contêiner automaticamente na nuvem quando você confirmar o código-fonte para um repositório Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 71ea0f489df6969f0916ac14d187e10a90a520cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722704"
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Tutorial: automatizar builds de imagem de contêiner com o Build de Registro de Contêiner do Azure

Além do [Quick Build](container-registry-tutorial-quick-build.md), o Build do ACR suporta builds de imagem de contêiner de Docker automatizados com a *tarefa de build*. Neste tutorial, você pode usar a CLI do Azure para criar uma tarefa de build que dispara automaticamente os builds de imagem na nuvem quando você confirma o código-fonte para um repositório Git.

Neste tutorial, parte dois da série:

> [!div class="checklist"]
> * Criar uma tarefa de build
> * Testar a tarefa de build
> * Exibir status do build
> * Disparar a tarefa de build com uma confirmação de código

Este tutorial presume que você já tenha concluído as tarefas no [tutorial anterior](container-registry-tutorial-quick-build.md). Se você ainda não tiver feito isso, conclua as etapas na seção [Pré-requisitos](container-registry-tutorial-quick-build.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser usar a CLI do Azure localmente, você deverá ter a versão da CLI do Azure **2.0.32** ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou fazer upgrade da CLI, confira [Instalar a CLI do Azure][azure-cli].

## <a name="prerequisites"></a>pré-requisitos

### <a name="get-sample-code"></a>Obter código de exemplo

Este tutorial presume que você já tenha concluído as tarefas no [tutorial anterior](container-registry-tutorial-quick-build.md) e tenha criado o fork para e clonado o repositório de exemplo. Se você ainda não tiver feito isso, conclua as etapas na seção [Pré-requisitos](container-registry-tutorial-quick-build.md#prerequisites) do tutorial anterior antes de continuar.

### <a name="container-registry"></a>Registro de contêiner

Você deve ter um registro de contêiner do Azure em sua assinatura do Azure para concluir este tutorial. Se você precisar de um registro, consulte o [tutorial anterior](container-registry-tutorial-quick-build.md) ou [Início Rápido: criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Tarefa de build

Uma tarefa de build define as propriedades de um build automatizado, incluindo o local do código-fonte da imagem de contêiner e o evento que aciona o build. Quando ocorre um evento definido na tarefa de build, como uma confirmação para um repositório Git, o Build do ACR inicia um build da imagem de contêiner na nuvem. Por padrão, em seguida, ele envia uma imagem criada com êxito para o registro de contêiner do Azure especificado na tarefa.

O Build do ACR atualmente suporta os seguintes gatilhos de tarefa de build:

* Confirmar para um repositório Git
* Atualização de imagem base

## <a name="create-a-build-task"></a>Criar uma tarefa de build

Nesta seção, você criará primeiro um token de acesso pessoal (PAT) do GitHub para uso com o Build do ACR. Em seguida, você criar uma tarefa de build que dispara um build quando o código é confirmado para seu fork do repositório.

### <a name="create-a-github-personal-access-token"></a>Criar um token de acesso pessoal do GitHub

Para disparar um build em uma confirmação para um repositório Git, o Build do ACR precisa de um token de acesso pessoal (PAT) para acessar o repositório. Siga estas etapas para gerar um PAT no GitHub:

1. Navegue até a página de criação do PAT no GitHub em https://github.com/settings/tokens/new
1. Digite uma **descrição** curta para o token, por exemplo, "Demonstração de tarefa de Build do ACR"
1. Em **repo**, habilite **repo:status** e **public_repo**

   ![Captura de tela da página de geração do Token de Acesso Pessoal no GitHub][build-task-01-new-token]

1. Selecione o botão **Gerar token** (você pode ser solicitado a confirmar sua senha)
1. Copie e salve o token gerado em um **local seguro** (use esse token quando você definir uma tarefa de build na seção a seguir)

   ![Captura de tela do Token de Acesso Pessoal no GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Criar a tarefa de build

Agora que você concluiu as etapas necessárias para habilitar o Build do ACR para ler o status de confirmação e criar webhooks em um repositório, você pode criar uma tarefa de build que dispara um build de imagem de contêiner em confirmações para o repositório.

Primeiro, preencha essas variáveis de ambiente de shell com valores apropriados para seu ambiente. Isso não é estritamente necessário, mas torna a execução dos comandos de CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se não preenchê-las, você deverá substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Agora, crie a tarefa de compilação executando o seguinte comando [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Esta tarefa de build especifica que qualquer código de tempo é confirmado para o branch *master* no repositório especificado por `--context`, o Build do ACR compilará a imagem de contêiner do código presente nessa ramificação. O argumento `--image` especifica um valor com parâmetros de `{{.Build.ID}}` para a parte da versão de marca da imagem, garantindo que a imagem criada esteja correlacionada a um build específico e seja marcada exclusivamente.

A saída de um comando [az acr build-task create][az-acr-build-task-create] bem-sucedido é semelhante à seguinte:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Testar a tarefa de build

Agora, você tem uma tarefa de build que define seu build. Para testar a definição de compilação, dispare um build manualmente executando o comando [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Por padrão, o comando `az acr build-task run` transmite a saída de log para o console quando você executa o comando. Aqui, a saída mostra que o build **aa2** foi colocado na fila e compilado.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Exibir status do build

Você pode ocasionalmente achar útil exibir o status de um build em andamento que não disparou manualmente. Por exemplo, ao solucionar problemas do builds disparados por confirmações de código-fonte. Nesta seção, você dispara um build manual, mas suprime o comportamento padrão de transmitir o log de build para o console. Em seguida, você usa o comando `az acr build-task logs` para monitorar o build em andamento.

Primeiro, dispare um build manualmente como você fez anteriormente, mas especifique o argumento `--no-logs` para suprimir o registro em log para o console:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Em seguida, use o comando `az build-task logs` para exibir o log do build em execução no momento:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

O log para o build em execução no momento é transmitido para o console e deve ser semelhante à seguinte saída (mostrada aqui truncada):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Disparar um build com uma confirmação

Agora que você testou a tarefa de build executando-a manualmente, dispare-a automaticamente com uma alteração de código-fonte.

Primeiro, certifique-se de que você esteja no diretório que contém o clone local do [repositório][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Em seguida, execute os seguintes comandos para criar, confirmar e enviar um novo arquivo para o seu fork do repositório no GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Você pode ser solicitado a fornecer suas credenciais do GitHub quando executar o comando `git push`. Forneça seu nome de usuário do GitHub e insira o token de acesso pessoal (PAT) que criou anteriormente para a senha.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Depois que você tiver enviado uma confirmação para o repositório, o webhook criado pelo Build do ACR dispara e desativa um build no Registro de Contêiner do Azure. Exiba os logs de compilação para o build em execução no momento verificar e monitorar o progresso do build:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

A saída é semelhante à seguinte, mostrando o build em execução (ou executado por último):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Listar builds

Para ver uma lista dos builds que o Build do ACR concluiu para o registro, execute o comando [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

A saída do comando deve ser semelhante ao seguinte. Os builds executados pelo Build do ACR são exibidos, e "Confirmação de Git" aparece na coluna TRIGGER para o build mais recente:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá como usar uma tarefa de build para disparar builds de imagem de contêiner automaticamente no Azure quando você confirmar o código-fonte para um repositório Git. Vá para o próximo tutorial para aprender a criar tarefas de build que disparam builds quando a imagem de base da imagem de um contêiner é atualizada.

> [!div class="nextstepaction"]
> [Automatizar builds na atualização da imagem de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
