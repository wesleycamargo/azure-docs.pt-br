---
title: "Tutorial sobre Instâncias de Contêiner do Azure – preparar o Registro de Contêiner do Azure"
description: "Tutorial sobre Instâncias de Contêiner do Azure, parte 2 de 3 – preparar o Registro de Contêiner do Azure"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Implantar e usar o Registro de Contêiner do Azure

Esta é a parte dois de um tutorial de três partes. Na [etapa anterior](container-instances-tutorial-prepare-app.md), uma imagem de contêiner foi criada para um aplicativo Web simples escrito em [Node.js][nodejs]. Neste tutorial, você pode enviar a imagem para um Registro de Contêiner do Azure. Se você não criou a imagem de contêiner, retorne ao [Tutorial 1 – Criar imagem de contêiner](container-instances-tutorial-prepare-app.md).

O Registro de Contêiner do Azure é um registro privado baseado no Azure para imagens de contêiner do Docker. Este tutorial demonstra a implantação de uma instância do Registro de Contêiner do Azure e o envio por push de uma imagem de contêiner a ele.

Neste artigo, a segunda parte da série, você:

> [!div class="checklist"]
> * Implanta uma instância do Registro de Contêiner do Azure
> * Marca uma imagem de contêiner para o Registro de Contêiner do Azure
> * Carrega a imagem em seu registro

No próximo artigo, que é o último da série, você implanta o contêiner do seu registro particular nas Instâncias de Contêiner do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer a execução da CLI do Azure versão 2.0.23 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure][azure-cli-install].

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Você precisa instalar o ambiente de desenvolvimento do Docker e a CLI do Azure no computador local para concluir este tutorial.

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create]. Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie um registro de contêiner do Azure com o comando [az acr create][az-acr-create]. O nome do Registro de contêiner deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Substitua `<acrName>` por um nome exclusivo para o registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Por exemplo, para criar um Registro de contêiner do Azure chamado *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Durante o restante deste tutorial, utilizamos `<acrName>` como um espaço reservado para o nome do registro de contêiner escolhido.

## <a name="container-registry-login"></a>Logon no registro de contêiner

Você deve fazer logon na instância do Registro de Contêiner do Azure antes de enviar imagens por push a ela. Use o comando [az acr login][az-acr-login] para concluir a operação. Você deve fornecer o nome exclusivo que foi fornecido para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem `Login Succeeded` na conclusão.

## <a name="tag-container-image"></a>Marcar imagem de contêiner

Para implantar uma imagem de contêiner de um registro privado, você precisa marcar a imagem com o nome de `loginServer` do registro.

Para consultar uma lista de imagens atuais, utilize o comando [docker images][docker-images].

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Para obter o nome de loginServer, execute o comando [az acr show][az-acr-show]. Substitua `<acrName>` pelo nome do seu Registro de contêiner.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Saída de exemplo:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Marque a imagem *aci-tutorial-app* com o loginServer do Registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Essa marca indica o número de versão da imagem. Substitua `<acrLoginServer>` pelo resultado do comando [az acr show][az-acr-show] que você acabou de executar.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Depois de marcar, execute `docker images` para verificar a operação.

```bash
docker images
```

Saída:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Envie por push a imagem *aci-tutorial-app* ao registro com o comando [docker push][docker-push]. Substitua `<acrLoginServer>` pelo nome do servidor de logon completo obtido na etapa anterior.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A operação `push` deve levar de alguns segundos a alguns minutos, dependendo de sua conexão de Internet, e a saída é semelhante à seguinte:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Listar imagens no Registro de Contêiner do Azure

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list][az-acr-repository-list]. Atualize o comando com o nome do registro de contêiner.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```azurecli
Result
----------------
aci-tutorial-app
```

E, em seguida, para ver as marcas de uma imagem específica, use o comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Saída:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você preparou um Registro de Contêiner do Azure para ser usado com Instâncias de Contêiner do Azure e enviou por push uma imagem de contêiner ao registro. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância de Registro de Contêiner do Azure
> * Uma imagem de contêiner foi marcada para o Registro de Contêiner do Azure
> * Uma imagem foi carregada no Registro de Contêiner do Azure

Avance para o próximo tutorial para saber mais sobre a implantação do contêiner no Azure usando as Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Implantar contêineres nas Instâncias de Contêiner do Azure](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
