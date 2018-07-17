---
title: Tutorial - Criar imagens de contêiner na nuvem com o Build do Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá como criar uma imagem de contêiner de Docker no Azure com o Build do Registro de Contêiner do Azure (Build do ACR) e implantá-la em Instâncias de Contêiner do Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461462"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Tutorial: criar imagens de contêiner na nuvem com o Build do Registro de Contêiner do Azure

**Build do ACR** é um pacote de recursos dentro do Registro de Contêiner do Azure que fornece builds de contêiner do Docker aprimorados e eficientes no Azure. Neste artigo, você aprenderá a usar o recurso *Quick Build* do ACR do Build. O Quick Build estende o "loop interno" de desenvolvimento para a nuvem, fornecendo validação de sucesso de build e envio automático de imagens criadas com êxito para o registro do contêiner. As imagens são criadas nativamente na nuvem, perto de seu registro, permitindo a implantação mais rápida.

Todos os seus conhecimentos do Dockerfile são transferíveis diretamente para o Build do ACR. Você não precisa alterar seus Dockerfiles para criar na nuvem com o Build do ACR, apenas o comando que você executa.

Neste tutorial, a primeira parte de uma série:

> [!div class="checklist"]
> * Obter o código-fonte do aplicativo de exemplo
> * Criar uma imagem de contêiner no Azure
> * Implantar um contêiner em Instâncias de Contêiner do Azure

Em tutoriais subsequentes, você aprenderá a usar tarefas de build do Build do ACR para builds de imagem de contêiner automatizados na atualização de confirmação de código e imagem de base.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser usar a CLI do Azure localmente, você deverá ter a versão da CLI do Azure **2.0.32** ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou fazer upgrade da CLI, confira [Instalar a CLI do Azure][azure-cli].

## <a name="prerequisites"></a>pré-requisitos

### <a name="github-account"></a>Conta do GitHub

Crie uma conta no https://github.com caso você ainda não tenha uma. Esta série de tutoriais usa um repositório GitHub para demonstrar os builds de imagem automatizados no Build do ACR.

### <a name="fork-sample-repository"></a>Repositório de exemplo de fork

Em seguida, use a interface do usuário do GitHub para dividir o repositório de exemplo em sua conta do GitHub. Neste tutorial, você cria uma imagem de contêiner a partir da origem no repositório e, no tutorial seguinte, envie uma confirmação para o seu fork do repositório para iniciar um build automatizado.

Crie fork para este repositório: https://github.com/Azure-Samples/acr-build-helloworld-node

![Captura de tela do botão Fork (realçado) no GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Clonar seu fork

Depois que você tiver criado o fork do repositório, clone o fork e entre no diretório que contém o clone local.

Clone o repositório com `git`, substitua **\<your-github-username\>** pelo seu nome de usuário do GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Insira o diretório que contém o código-fonte:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Shell do Bash

Os comandos nesta série de tutoriais são formatados para o shell de Bash. Se você preferir usar o PowerShell, o Prompt de Comando ou outro shell, convém ajustar a continuação da linha e o formato da variável de ambiente adequadamente.

## <a name="build-in-azure-with-acr-build"></a>Criar no Azure com o Build do ACR

Agora que você efetuou o pull do código-fonte para o seu computador, siga estas etapas para criar um registro de contêiner e criar a imagem de contêiner com o Build do ACR.

Para tornar a execução dos comandos de exemplo mais fácil, os tutoriais desta série usam variáveis de ambiente de shell. Execute o seguinte comando para definir a variável `ACR_NAME`. Substitua **\<registry-name\>** por um nome exclusivo para seu novo registro de contêiner. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Os outros recursos que você cria no tutorial se baseiam nesse nome, e você precisará modificar apenas essa primeira variável.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Com a variável de ambiente de registro de contêiner preenchida, agora você deverá ser capaz de copiar e colar o restante dos comandos no tutorial sem editar valores. Execute os comandos a seguir para criar um registro de contêiner e um grupo de recursos:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Agora que você tem um registro, use o Build do ACR para criar uma imagem de contêiner a partir do código de exemplo. Execute o comando [az acr build][az-acr-build] para executar um *Quick Build*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

A saída do comando [az acr build][az-acr-build] é semelhante ao seguinte. Você pode ver o upload do código-fonte (o "contexto") para o Azure e os detalhes da operação `docker build` que o Build do ACR executa na nuvem. Como o Build do ACR usa `docker build` para criar suas imagens, nenhuma alteração nos seus Dockerfiles é necessária para começar a usar o Build do ACR imediatamente.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

No final da saída, o Build do ACR exibe as dependências que ele descobriu para a imagem. Isso permite que o Build do ACR automatize os builds de imagem em atualizações de imagem de base, como quando uma imagem de base é atualizada com os patches de sistema operacional ou estrutura. Você aprenderá sobre o suporte do Build do ACR para atualizações de imagem de base mais tarde nesta série de tutoriais.

## <a name="deploy-to-azure-container-instances"></a>Implantar para Instâncias de Contêiner do Azure

O Build do ACR automaticamente envia imagens criadas com êxito para o registro por padrão, permitindo que sejam implantadas a partir do registro imediatamente.

Nesta seção, você cria um Azure Key Vault e a entidade de serviço e depois implanta o contêiner nas Instâncias de Contêiner do Azure (ACI) usando as credenciais da entidade de serviço.

### <a name="configure-registry-authentication"></a>Configurar a autenticação do registro

Todos os cenários de produção devem usar [entidades de serviço][service-principal-auth] para acessar um registro de contêiner do Azure. As entidades de serviço permitem que você forneça controle de acesso baseado em função às imagens de contêiner. Por exemplo, é possível configurar uma entidade de serviço com acesso somente pull para um registro.

#### <a name="create-key-vault"></a>Criar cofre chaves

Se você ainda não tiver um cofre no [Azure Key Vault](/azure/key-vault/), crie um com a CLI do Azure usando os comandos a seguir.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Criar entidades de serviço e armazenar credenciais

Agora, é necessário criar uma entidade de serviço e armazenar as credenciais no cofre de chaves.

Use o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar a entidade de serviço e [az keyvault secret set][az-keyvault-secret-set] para armazenar a **senha** da entidade de serviço no cofre:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

O argumento `--role` no comando anterior configura a entidade de serviço com a função *leitor*, que concede a acesso somente pull ao registro. Para conceder acesso push e pull, altere o argumento `--role` para *colaborador*.

Em seguida, armazene o *appId* da entidade de serviço no cofre, que é o **nome de usuário** que você passa para o Registro de Contêiner do Azure para autenticação:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Você criou um Azure Key Vault e armazenou dois segredos nele:

* `$ACR_NAME-pull-usr`: a ID da entidade de serviço, para uso como o **nome de usuário** do registro de contêiner.
* `$ACR_NAME-pull-pwd`: a senha da entidade de serviço, para uso como a **senha** do registro de contêiner.

Agora, é possível referenciar esses segredos pelo nome quando você ou os aplicativos e serviços efetuam pull de imagens do registro.

### <a name="deploy-container-with-azure-cli"></a>Implantar contêiner com CLI do Azure

Agora que as credenciais da entidade de serviço estão armazenadas como segredos do Azure Key Vault, os aplicativos e serviços podem utilizá-las para acessar o registro privado.

Execute o seguinte comando [az container create][az-container-create] para implantar uma instância de contêiner. O comando usa as credenciais da entidade de serviço armazenadas no Azure Key Vault para autenticar no registro de contêiner.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

O valor `--dns-name-label` deve ser exclusivo no Azure, portanto, o comando anterior acrescenta o nome do registro de contêiner ao rótulo do nome DNS do contêiner. A saída do comando exibe o FQDN (nome de domínio totalmente qualificado) do contêiner, por exemplo:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Anote o FQDN do contêiner, você o usará na próxima seção.

### <a name="verify-deployment"></a>Verificar implantação

Para inspecionar o processo de inicialização do contêiner, use o comando [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

A saída `az container attach` exibe primeiro o status do contêiner conforme ele efetua pull da imagem e é iniciado, em seguida, associa STDOUT e STDERR do console local para o do contêiner.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Quando `Server running at http://localhost:80` aparecer, navegue para o FQDN do contêiner no seu navegador para ver o aplicativo em execução:

![Captura de tela do aplicativo de exemplo renderizado no navegador][quick-build-02-browser]

Para desanexar o console do contêiner, pressione `Control+C`.

## <a name="clean-up-resources"></a>Limpar recursos

Pare a instância de contêiner com o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Para remover *todos* os recursos que você criou neste tutorial, incluindo o registro de contêiner, o cofre de chaves e a entidade de serviço, emita os seguintes comandos. Esses recursos são usados no [tutorial seguinte](container-registry-tutorial-build-task.md) da série, no entanto, será conveniente mantê-los se você passar diretamente para o próximo tutorial.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Agora que você testou o loop interno com um build rápido, configure uma **tarefa de build** para disparar builds de imagens de contêiner quando você confirmar o código-fonte para um repositório Git:

> [!div class="nextstepaction"]
> [Disparar builds automáticos com tarefas de build](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
