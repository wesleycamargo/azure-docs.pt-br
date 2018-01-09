---
title: "Início Rápido – Criar o primeiro contêiner de Instâncias de Contêiner do Azure"
description: "Implantar e começar a trabalhar com as Instâncias de Contêiner do Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: bf511f60a431a110f43d26444dedb7728b040af5
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o primeiro contêiner nas Instâncias de Contêiner do Azure
As Instâncias de Contêiner do Azure facilitam criar e gerenciar contêineres do Docker no Azure, sem a necessidade de provisionar máquinas virtuais ou adotar um serviço de nível superior. Neste início rápido, você cria um contêiner no Azure e o expõe à Internet com um endereço IP público. Essa operação é concluída com um único comando. Em poucos segundos, você verá o seguinte em seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.21 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As Instâncias de Contêiner do Azure são recursos do Azure e devem ser colocadas em um grupo de recursos do Azure, um conjunto lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create].

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contêiner

Você pode criar um contêiner fornecendo um nome, uma imagem do Docker e um grupo de recursos do Azure para o comando [az container create][az-container-create]. Opcionalmente, você pode expor o contêiner à Internet com um endereço IP público. Neste guia de início rápido, você implanta um contêiner que hospeda um pequeno aplicativo Web escrito no [Node.js][node-js].

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --ip-address public --ports 80
```

Em alguns segundos, você deve obter uma resposta à solicitação. Inicialmente, o contêiner fica no estado **Criando**, mas ele deve começar em alguns segundos. Você pode verificar o status usando o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Na parte inferior da saída, você verá o estado de provisionamento do contêiner e seu endereço IP:

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Depois que o contêiner muda para o estado **Êxito**, você pode acessá-lo no navegador, usando o endereço IP fornecido.

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

## <a name="pull-the-container-logs"></a>Acessar os logs de contêiner

Você pode acessar os logs para o contêiner criado usando o comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Saída:

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>Excluir o contêiner

Quando você conclui o contêiner, pode removê-lo usando o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para verificar se o contêiner foi excluído, execute o comando [az container list](/cli/azure/container#az_container_list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

O contêiner **mycontainer** não deve aparecer na saída do comando. Se você não tiver outros contêineres no grupo de recursos, não será exibida nenhuma saída.

## <a name="next-steps"></a>Próximas etapas

Todo o código para o contêiner usado neste início rápido está disponível [no GitHub][app-github-repo], juntamente com seu Dockerfile. Se você quiser tentar criá-lo e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutoriais sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

Para experimentar as opções para contêineres em execução em um sistema de orquestração no Azure, veja os inícios rápidos do [Service Fabric][service-fabric] ou do [Serviço de Contêiner do Azure (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
