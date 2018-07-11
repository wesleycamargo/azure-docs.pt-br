---
title: Tutorial do Kubernetes no Azure - atualizar cluster
description: Tutorial do Kubernetes no Azure - atualizar cluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131636"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutorial: fazer upgrade do Kubernetes no Serviço de Kubernetes do Azure (AKS)

Um cluster AKS (Serviço de Kubernetes do Azure) pode ser atualizado usando a CLI do Azure. Durante o processo de upgrade, os nós Kubernetes são cuidadosamente [isolados e esvaziados][kubernetes-drain] para minimizar as interrupções nos aplicativos em execução.

Neste tutorial, parte sete de sete, é feito o upgrade de um cluster Kubernetes. As tarefas a serem concluídas incluem:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. Em seguida, o aplicativo foi executado no cluster Kubernetes.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Obter versões de cluster

Antes de atualizar um cluster, use o comando [az aks get-upgrades][] para verificar quais versões do Kubernetes estão disponíveis para upgrade.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Neste exemplo, a versão atual do nó é *1.9.6* e as versões de atualização disponíveis ficam exibidas na coluna *Atualizações*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Fazer upgrade do cluster

Use o comando [az aks upgrade][] para fazer upgrade dos nós de cluster. Os exemplos a seguir atualizam o cluster para a versão *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

A saída condensada do exemplo a seguir mostra que, agora, *kubernetesVersion* informa *1.10.3*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Validar o upgrade

Confirme se o upgrade teve êxito com o comando [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez upgrade do Kubernetes em um cluster do AKS. Concluímos as seguintes tarefas:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

Siga este link para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Visão geral do AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade