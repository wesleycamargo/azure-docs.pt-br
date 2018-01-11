---
title: "Guia de início rápido – criar um registro particular do Docker no Azure com a CLI do Azure"
description: "Aprenda rapidamente a criar um registro de contêiner particular do Docker com a CLI do Azure."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Criar um registro de contêiner usando a CLI do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando a CLI do Azure.

Este guia de início rápido exige que você esteja executando a versão 2.0.21 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure][azure-cli].

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste guia de início rápido, criamos um registro *Básico*. O Registro de Contêiner do Azure está disponível em várias SKUs diferentes, descritos brevemente na tabela a seguir. Para obter detalhes estendidos sobre cada um, consulte [SKUs de registro de contêiner][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Crie uma instância ACR usando o comando [az acr create][az-acr-create].

O nome do registro **deve ser exclusivo**. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Quando o registro é criado, o resultado é semelhante ao seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

No restante deste guia de início rápido, utilizamos `<acrName>` como um espaço reservado para o nome do registro de contêiner.

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Para fazer isso, use o comando [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem `Login Succeeded` na conclusão.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se você ainda não tiver as imagens de contêiner locais, execute o comando a seguir para efetuar pull de uma imagem existente de Hub do Docker.

```bash
docker pull microsoft/aci-helloworld
```

Antes de poder enviar uma imagem por push no registro, você deve marcá-lo com o nome totalmente qualificado do seu servidor de logon ACR. Execute o comando a seguir para obter o nome completo do servidor de logon da instância do ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marque a imagem usando o comando [docker tag][docker-tag]. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por fim, use [docker push][docker-push] para enviar a imagem por push para a instância do ACR. Substitua o `<acrLoginServer>` pelo nome do servidor de logon da sua instância do ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Listar imagens de contêiner

O exemplo a seguir lista os repositórios em um registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```bash
Result
----------------
aci-helloworld
```

O exemplo a seguir lista as marcas no repositório **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Saída:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete][az-group-delete] para remover o grupo de recursos, a instância do ACR e todas as imagens de contêiner.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você ciou um Registro de Contêiner do Azure com a CLI do Azure. Se você quiser usar o Registro de Contêiner do Azure com Instâncias de Contêiner do Azure, prossiga para o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md