---
title: Início Rápido – Criar o primeiro contêiner de Instâncias de Contêiner do Azure
description: Neste guia de início rápido, você usa a CLI do Azure para implantar um contêiner em Instâncias de Contêiner do Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 07632e85719e2d0d446b8f718dbc64d2e9d77617
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441356"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Início Rápido: Criar o primeiro contêiner nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam criar e gerenciar contêineres do Docker no Azure, sem a necessidade de provisionar máquinas virtuais ou adotar um serviço de nível superior. Neste início rápido, você cria um contêiner no Azure e o expõe à Internet com um FQDN (nome de domínio totalmente qualificado). Essa operação é concluída com um único comando. Em poucos segundos, você verá o seguinte em seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.27 ou posterior. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As instâncias de contêiner do Azure, assim como todos os recursos do Azure, devem ser colocadas em um grupo de recursos, um conjunto lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create].

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contêiner

Você pode criar um contêiner fornecendo um nome, uma imagem do Docker e um grupo de recursos do Azure para o comando [az container create][az-container-create]. Outra opção é expor o contêiner à Internet, especificando um rótulo de nome DNS. Neste guia de início rápido, você implanta um contêiner que hospeda um pequeno aplicativo Web escrito no [Node.js][node-js].

Execute o comando a seguir para iniciar uma instância do contêiner. O valor `--dns-name-label` deve ser exclusivo dentro da região do Azure na qual você criar a instância, portanto, talvez seja preciso modificar esse valor para garantir a exclusividade.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Em alguns segundos, você deve obter uma resposta à solicitação. Inicialmente, o contêiner fica no estado **Criando**, mas ele deve começar em alguns segundos. Você pode verificar o status usando o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Quando você executar o comando, o nome de domínio totalmente qualificado (FQDN) do contêiner e seu estado de provisionamento são exibidos:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Depois que o contêiner muda para o estado **Êxito**, navegue até o FDQN no seu navegador:

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Acessar os logs de contêiner

Exibir os logs para uma instância de contêiner é útil ao solucionar problemas com o contêiner ou o aplicativo que é executado.

Retire os logs do contêiner com o comando[az container logs] [ az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A saída exibe os logs para o contêiner e deve mostrar as solicitações HTTP GET geradas quando o aplicativo é exibido no navegador.

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

Além de seguir os logs, você pode anexar sua saída padrão local e fluxos de erro padrão aos do contêiner.

Primeiro, execute o comando [az container attach][az-container-attach] para anexar seu console local os fluxos de saída do contêiner:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

Depois de anexados, atualize seu navegador algumas vezes para gerar algumas saídas adicionais. Por fim, desanexe o console com `Control+C`. Você deverá ver uma saída semelhante ao seguinte:

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você conclui o contêiner, remova-o usando o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para verificar se o contêiner foi excluído, execute o comando [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

O contêiner **mycontainer** não deve aparecer na saída do comando. Se você não tiver outros contêineres no grupo de recursos, não será exibida nenhuma saída.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma instância de Contêiner do Azure com base em uma imagem em no registro do Hub do Docker público. Se você quiser tentar criar uma imagem de contêiner sozinho e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

Para experimentar as opções para contêineres em execução em um sistema de orquestração no Azure, veja os inícios rápidos do [Service Fabric][service-fabric] ou do [Serviço de Kubernetes do Azure (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
