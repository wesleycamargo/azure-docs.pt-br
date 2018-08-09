---
title: Guia de início rápido – criar um registro particular do Docker no Azure com a CLI do Azure
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker com a CLI do Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 78dc9eceba11ce07deb7fe0d10df1fea9cd74a75
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426104"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Início Rápido: criar um registro de contêiner usando a CLI do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando a CLI do Azure, o envio por push de uma imagem de contêiner para o registro e, por fim, a implantação do contêiner a partir do registro nas ACI (Instâncias de Contêiner do Azure).

Este início rápido exige que você esteja executando a versão 2.0.27 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste início rápido, você cria um registro *Básico*. O Registro de Contêiner do Azure está disponível em várias SKUs diferentes, descritos brevemente na tabela a seguir. Para obter detalhes estendidos sobre cada um, consulte [SKUs de registro de contêiner][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Crie uma instância ACR usando o comando [az acr create][az-acr-create].

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

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

No restante deste início rápido, `<acrName>` é um espaço reservado para o nome do registro de contêiner.

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

## <a name="deploy-image-to-aci"></a>Implantar imagem nas ACI

Para implantar uma instância de contêiner a partir do registro que você criou, forneça as credenciais de registro quando implantá-la. Em cenários de produção, você deve usar uma [entidade de serviço] [ container-registry-auth-aci] para o acesso ao registro do contêiner, mas, para manter este início rápido breve, habilite o usuário administrador no seu registro com o seguinte comando:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Depois que o administrador for habilitado, o nome de usuário será o mesmo que o nome do registro, e você poderá recuperar a senha com este comando:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implantar a imagem de contêiner com um núcleo de CPU e 1 GB de memória, execute o comando a seguir. Substitua `<acrName>`, `<acrLoginServer>` e `<acrPassword>` pelos valores obtidos dos dois comandos anteriores.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Você deve obter uma resposta inicial do Azure Resource Manager com detalhes sobre o contêiner. Para monitorar o status do contêiner e verificar e visualizar quando ele está em execução, repita o [az container show][az-container-show]. Isso deve levar menos de um minuto.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Exibir o aplicativo

Depois que a implantação das ACI for bem-sucedida, recupere o FQDN do contêiner com o comando [az container show] [ az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Saída de exemplo: `"aci-demo.eastus.azurecontainer.io"`

Para ver o aplicativo em execução, navegue até o endereço IP público no seu navegador favorito.

![Aplicativo Olá, Mundo no navegador][aci-app-browser]

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete][az-group-delete] para remover o grupo de recursos, a instância do ACR e todas as imagens de contêiner.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Registro de Contêiner do Azure com a CLI do Azure, enviou uma imagem de contêiner para o registro e iniciou uma instância dele por meio das Instâncias de Contêiner do Azure. Continue com o tutorial das Instâncias de Contêiner do Azure a fim de ver as ACI com mais detalhes.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md