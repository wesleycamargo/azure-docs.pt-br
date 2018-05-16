---
title: Tutorial - automatizar builds de imagem de contêiner na atualização da imagem base com o Build de Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá como configurar uma tarefa de build para disparar builds de imagem de contêiner automaticamente na nuvem quando uma imagem base é atualizada.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 976f61d99b88d241b39bfec9d95e16de272d9c14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Tutorial: automatizar builds de imagem na atualização da imagem base com o Build de Registro de Contêiner do Azure

O Build do ACR dá suporte à execução de compilação automatizada quando a imagem base do contêiner é atualizada, como quando você corrige o sistema operacional ou a estrutura de aplicativo em uma das suas imagens de base. Neste tutorial, você aprenderá como criar uma tarefa de compilação no Build do ACR que dispara uma compilação na nuvem quando a imagem base do contêiner foi introduzida no registro.

Neste tutorial, o último da série:

> [!div class="checklist"]
> * Compilar a imagem base
> * Criar uma tarefa de compilação de imagem do aplicativo
> * Atualizar a imagem base para disparar uma compilação de imagem de aplicativo
> * Exibir a compilação disparada
> * Verificar a imagem do aplicativo atualizada

> [!IMPORTANT]
> O Build do ACR está atualmente em versão prévia e é suportado somente pelos registros de contêiner do Azure nas regiões **Leste dos EUA** (eastus) e **Europa Ocidental** (westeurope). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você quiser usar a CLI do Azure localmente, você deverá ter a versão da CLI do Azure **2.0.32** ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI do Azure 2.0][azure-cli].

## <a name="prerequisites"></a>pré-requisitos

### <a name="complete-previous-tutorials"></a>Concluir os tutoriais anteriores

Este tutorial pressupõe que você já concluiu as etapas nos dois primeiros tutoriais na série, em que você:

* Crie um registro de contêiner do Azure
* Cria fork do repositório de exemplo
* Clona o repositório de exemplo
* Cria um token de acesso pessoal do GitHub

Se você ainda não fez isso, conclua os dois primeiros tutoriais antes de continuar:

[Compilar imagens de contêiner na nuvem com o Build do Registro de Contêiner do Azure](container-registry-tutorial-quick-build.md)

[Automatizar builds de imagem de contêiner com o Build de Registro de Contêiner do Azure](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Configurar ambiente

Preencha essas variáveis de ambiente de shell com valores apropriados para seu ambiente. Isso não é estritamente necessário, mas torna a execução dos comandos de CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se não as preencher, você deverá substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Imagens base

Definindo a maioria das imagens de contêiner de Dockerfiles especifica uma imagem pai da qual ele se baseia, conhecida como sua *imagem base*. Imagens de base normalmente contêm o sistema operacional, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], no qual o resto do camadas do contêiner são aplicadas. Elas também podem incluir estruturas de aplicativo, como [Node.js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Atualizações de imagem base

Uma imagem de base geralmente é atualizada pelo mantenedor de imagem para incluir novos recursos ou aprimoramentos para o sistema operacional ou da estrutura na imagem. Patches de segurança são outra causa comum para uma atualização da imagem base.

Quando uma imagem de base é atualizada, você verá a necessidade de recriar as imagens de contêiner no registro com base nele para incluir os novos recursos e correções. O Build do ACR inclui a capacidade de criar imagens automaticamente para você quando a imagem base do contêiner é atualizada.

### <a name="base-image-update-scenario"></a>Cenário de atualização de imagem base

Este tutorial orienta você através de um cenário de atualização da imagem base. O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem de aplicativo e uma imagem que ele especifica como base. Nas seções a seguir, você criará uma tarefa de Build do ACR que aciona automaticamente uma compilação da imagem do aplicativo quando uma nova versão da imagem base é enviada para o registro de contêiner.

[Aplicativo de Dockerfile][dockerfile-app]: um pequeno aplicativo web Node.js que renderiza uma página da web estática exibindo a versão do Node.js no qual é baseado. A cadeia de caracteres de versão é simulada, em que mostra o conteúdo de uma variável de ambiente `NODE_VERSION`, que é definido na imagem base.

[Base de Dockerfile][dockerfile-base]: A imagem que `Dockerfile-app` especifica como base. É baseado em uma imagem de [Node][base-node] e inclui a variável de ambiente `NODE_VERSION`.

Nas seções a seguir, você cria uma tarefa de compilação, atualiza o valor `NODE_VERSION` na imagem base Dockerfile, em seguida, usa o Build do ACR para criar a imagem base. Quando o Build do ACR envia a nova imagem base para o registro, ele automaticamente aciona uma compilação da imagem do aplicativo. Opcionalmente, você pode executar a imagem de contêiner do aplicativo localmente para ver as cadeias de caracteres de versões diferentes nas imagens compiladas.

## <a name="build-base-image"></a>Compilar imagem base

Comece a compilar a imagem base com um *Compilar rápido* do Build do ACR. Como discutido no [primeiro tutorial](container-registry-tutorial-quick-build.md) da série, isso não só compila a imagem, mas a envia ao registro de contêiner, se a compilação for bem-sucedida.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Criar tarefa de compilação

Em seguida, crie uma tarefa de compilação com [Criar tarefa de compilação acr az][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Esta tarefa de compilação é semelhante à tarefa criada no [tutorial anterior](container-registry-tutorial-build-task.md). Ela instrui o Build do ACR para disparar uma compilação de imagem quando confirmações são enviados por push para o repositório especificado por `--context`.

Onde ele é diferente em seu comportamento, em que ele também irá disparar uma compilação da imagem quando sua *imagem base* é atualizada. O Dockerfile especificado pelo argumento `--file`, [Dockerfile aplicativo][dockerfile-app], dá suporte à especificação de uma imagem de dentro do mesmo registro como base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Quando você executa uma tarefa de compilação, o Build do ACR detecta dependências de uma imagem. Se a imagem base especificada na instrução `FROM` reside no mesmo registro, ela adiciona um gancho para garantir que esta imagem é reconstruída a qualquer momento em que sua base é atualizada.

> [!NOTE]
> Durante a versão prévia, o Build do ACR dá suporte ao disparar um build de imagem derivada somente quando a imagem base e a imagem que faz referência a ela residirem no mesmo registro de contêiner do Azure.

## <a name="build-application-container"></a>Compilar contêiner de aplicativo

Para habilitar o Build do ACR para determinar e controlar dependências de uma imagem do contêiner – que inclui sua imagem base – você **deve** primeiro disparar sua tarefa de compilação **pelo menos uma vez**.

Use [executar tarefa de compilação acr az][az-acr-build-task-run] para disparar a tarefa de compilação manualmente e compilar a imagem do aplicativo:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Concluída a compilação, anote a **ID do Build** (por exemplo, "eastus6") se você deseja concluir a seguinte etapa opcional.

### <a name="optional-run-application-container-locally"></a>Opcional: Executar contêiner do aplicativo localmente

Se você estiver trabalhando localmente (não no Cloud Shell), e você tiver o Docker instalado, execute o contêiner para ver o aplicativo renderizado em um navegador da web antes de recriar a imagem base. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `az acr login` ou `docker run`).

Primeiro, faça logon em seu registro de contêiner com [logon de acr az][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contêiner localmente com `docker run`. Substituir **\<identificação da compilação\>** pela ID de compilação encontrada na saída da etapa anterior (por exemplo, "eastus5").

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Navegue até http://localhost:8080 no seu navegador, e você deverá ver o número de versão do Node.js renderizado na página da web, semelhante à seguinte. Em uma etapa posterior, você deve aumentar a versão adicionando uma "a" para a cadeia de caracteres de versão.

![Captura de tela do aplicativo de exemplo renderizado no navegador][base-update-01]

## <a name="list-builds"></a>Listar builds

Em seguida, liste as compilações que o Build do ACR concluiu para o registro:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Se você concluiu o tutorial anterior (e não excluiu o registro), você verá uma saída semelhante à seguinte. Anote o número de compilações e a ID da compilação mais recente, portanto você pode comparar a saída depois de atualizar a imagem base na próxima seção.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
eastus6     buildhelloworld  Linux       Succeeded  Manual        2018-04-22T00:03:46Z  00:00:40
eastus5                                  Succeeded  Manual        2018-04-22T00:01:45Z  00:00:25
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-21T23:52:33Z  00:00:30
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:50:10Z  00:00:35
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:46:15Z  00:00:55
eastus1                                  Succeeded  Manual        2018-04-21T23:24:05Z  00:00:35
```

## <a name="update-base-image"></a>Atualização de imagem base

Aqui você pode simular um patch de estrutura na imagem base. Edite **Dockerfile-base**e adicione um "a" depois do número de versão definido em `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Execute uma compilação rápida no Build do ACR para compilar a imagem base modificada. Anote a **ID do Build** na saída.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Quando a compilação estiver concluída e o Build do ACR enviar a nova imagem base para o registro, ele aciona uma compilação da imagem do aplicativo. Pode levar alguns instantes para que a tarefa do Build do ACR criado anteriormente dispare a compilação de imagem do aplicativo, porque ele deve detectar a imagem base recentemente enviada e concluída.

## <a name="list-builds"></a>Listar builds

Agora que você atualizou a imagem base, liste suas compilações novamente para comparar com a lista anterior. Se, primeiramente, a saída não diferir, execute periodicamente o comando para ver a nova compilação aparecer na lista.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

A saída deverá ser semelhante à seguinte. O GATILHO para a compilação executada por último deve ser "Atualização da Imagem", indicando que a tarefa de compilação foi iniciada pela sua compilação rápida da imagem base.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ----------    --------------------  ----------
eastus8     buildhelloworld  Linux       Succeeded  Image Update  2018-04-22T00:09:24Z  00:00:50
eastus7                                  Succeeded  Manual        2018-04-22T00:08:49Z  00:00:40
eastus6     buildhelloworld  Linux       Succeeded  Image Update  2018-04-20T00:15:30Z  00:00:43
eastus5     buildhelloworld  Linux       Succeeded  Manual        2018-04-20T00:10:05Z  00:00:45
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-19T23:40:38Z  00:00:40
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:36:37Z  00:00:40
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:35:27Z  00:00:40
eastus1                                  Succeeded  Manual        2018-04-19T22:51:13Z  00:00:30
```

Se você deseja executar a seguinte etapa opcional de executar o contêiner recém-criado para ver o número de versão atualizada, anote o valor da **ID do Build** para a compilação disparada para atualização da imagem (na saída acima, é "eastus6").

### <a name="optional-run-newly-built-image"></a>Opcional: Executar imagem criada recentemente

Se você estiver trabalhando localmente (não no Cloud Shell), e tiver o Docker instalado, execute a nova imagem de aplicativo após a conclusão da sua compilação. Substitua `<build-id>` pela ID do BUILD obtida na etapa anterior. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Navegue até http://localhost:8081 no seu navegador, e você deverá ver o número de versão do Node.js atualizado (com o “a”) na página da web:

![Captura de tela do aplicativo de exemplo renderizado no navegador][base-update-02]

O que é importante a observar é que você atualizou sua imagem **base** com um novo número de versão, mas a última imagem de **aplicativo** compilada exibe a nova versão. O Build do ACR pegou a alteração para a imagem base e recriou sua imagem de aplicativo automaticamente.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que você criou nesta série de tutoriais, incluindo o registro de contêiner, a instância do contêiner, o cofre de chaves e a entidade de serviço, emita os seguintes comandos:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como usar uma tarefa de compilação para disparar compilações de imagem de contêiner automaticamente na nuvem quando a imagem base da imagem foi atualizada. Agora, vá para saber mais sobre a autenticação para o registro de contêiner.

> [!div class="nextstepaction"]
> [Autenticação no Registro de Contêiner do Azure](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png