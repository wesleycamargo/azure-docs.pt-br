---
title: "Guia de início rápido - cluster Kubernetes do Azure para Linux | Microsoft Docs"
description: "Aprenda rapidamente a criar um cluster Kubernetes para contêineres do Linux no AKS com a CLI do Azure."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 89d469b330644b8f5b82a343ea4408d5b8d10b12
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Implantar um cluster do AKS (Serviço de Contêiner do Azure)

Neste início rápido, um cluster AKS é implantado usando a CLI do Azure. Um aplicativo de vários contêineres composto por um front-end da Web e uma instância Redis é executado no cluster. Depois de concluído, o aplicativo pode ser acessado pela internet.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este início rápido pressupõe uma compreensão básica dos conceitos de Kubernetes; para saber mais sobre Kubernetes, consulte a [documentação do Kubernetes]( https://kubernetes.io/docs/home/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Habilitando a versão prévia do AKS para sua assinatura do Azure
Enquanto o AKS está na versão prévia, a criação de novos clusters requer um sinalizador de recurso em sua assinatura. Você pode solicitar esse recurso para qualquer número de assinaturas que deseje usar. Use o comando `az provider register` para registrar o provedor do AKS:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Após o registro, você estará pronto para criar um cluster do Kubernetes com o AKS.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westus2*.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar cluster AKS

O exemplo a seguir cria um cluster chamado *myK8sCluster* com um nó.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8sCluster --agent-count 1 --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster Kubernetes, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando Kubernetes.

Se você estiver usando o Azure Cloud Shell, o kubectl já estará instalado. Se desejar instalá-lo localmente, execute o comando a seguir.


```azurecli
az aks install-cli
```

Para configurar o kubectl e se conectar ao cluster Kubernetes, execute o comando a seguir. Esta etapa baixa credenciais e configura a CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myK8sCluster
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para retornar uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

Saída:

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto Kubernetes define um estado desejado para o cluster, incluindo as imagens de contêiner que devem estar em execução. Neste exemplo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo Azure Vote.

Crie um arquivo chamado `azure-vote.yml` e copie-o para o código YAML a seguir. Se você estiver trabalhando no Azure Cloud Shell, esse arquivo poderá ser criado usando o vi ou Nano, como se estivesse trabalhando em um sistema físico ou virtual.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Use o comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para executar o aplicativo.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Saída:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar o aplicativo

Conforme o aplicativo é executado, um [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) é criado para expor o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL -IP* para o serviço *azure-vote-front* aparece como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Depois que o endereço *EXTERNAL-IP* for alterado de *pendente* para um *endereço IP*, use `CTRL-C` para interromper o processo kubectl watch.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Agora você pode navegar para o endereço IP externo a fim de ver o aplicativo Azure Vote.

![Imagem de navegação para o Voto do Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste guia de início rápido, foram usadas imagens de contêiner criadas previamente para criar uma implantação de Kubernetes. O código de aplicativo relacionado, o Dockerfile e o arquivo de manifesto Kubernetes estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster Kubernetes e um aplicativo de com vários contêineres nele.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gerenciar um cluster AKS](./tutorial-kubernetes-prepare-app.md)
