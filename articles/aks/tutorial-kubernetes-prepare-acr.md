---
title: "Tutorial do Kubernetes no Azure – Preparar o ACR"
description: "Tutorial do AKS – preparar o ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d436e7d9046fa9c1bced890c005f98b40b372ef6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implantar e usar o Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) é um registro privado baseado no Azure, para imagens de contêiner do Docker. Este tutorial, parte dois de oito, demonstra a implantação de uma instância de Registro de Contêiner do Azure e envia por push uma imagem de contêiner para ela. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantando uma instância de ACR (Registro de Contêiner do Azure)
> * Marcando uma imagem de contêiner para ACR
> * Carregando da imagem para ACR

Nos próximos tutoriais, essa instância de ACR será integrada a um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior][aks-tutorial-prepare-app], uma imagem de contêiner foi criada para um aplicativo de Votação do Azure simples. Se você não tiver criado a imagem do aplicativo de Votação do Azure, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial requer a execução da CLI do Azure versão 2.0.21 ou posterior. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create]. Neste exemplo, criaremos um grupo de recursos `myResourceGroup` na região `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie um Registro de contêiner do Azure com o comando [az acr create][az-acr-create]. O nome de um registro de contêiner **deve ser exclusivo**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Durante o restante deste tutorial, utilizamos `<acrName>` como um espaço reservado para o nome do registro de contêiner.

## <a name="container-registry-login"></a>Logon no registro de contêiner

Use o comando [az acr login][az-acr-login] para fazer logon na instância do ACR. Você precisa fornecer o nome exclusivo fornecido para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-images"></a>Marcar imagens de contêiner

Para consultar uma lista de imagens atuais, utilize o comando [docker images][docker-images].

```console
docker images
```

Saída:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Cada imagem de contêiner precisa ser marcada com o nome do registro loginServer. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para obter o nome de loginServer, execute o comando a seguir.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marque a imagem `azure-vote-front` com o loginServer do registro de contêiner. Além disso, adicione `:redis-v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Depois de marcada, execute [docker images][docker-images] para verificar a operação.

```console
docker images
```

Saída:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Envie a imagem `azure-vote-front` por push ao registro.

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Isso leva alguns minutos para ser concluído.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list][az-acr-repository-list]. Atualize o comando com o nome da instância do ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```azurecli
Result
----------------
azure-vote-front
```

E, em seguida, para ver as marcas de uma imagem específica, use o comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Saída:

```azurecli
Result
--------
redis-v1
```

Na conclusão do tutorial, a imagem de contêiner foi armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem será implantada do ACR para um cluster Kubernetes em tutoriais subsequentes.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um Registro de Contêiner do Azure foi preparado para ser usado em um cluster AKS. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância de Registro de Contêiner do Azure
> * Marcando uma imagem de contêiner para ACR
> * Carregando a imagem para ACR

Avance para o próximo tutorial para saber mais sobre a implantação de um cluster do Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implantar um cluster do Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md