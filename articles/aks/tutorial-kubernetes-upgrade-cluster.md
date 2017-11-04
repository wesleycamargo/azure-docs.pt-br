---
title: "Tutorial do Kubernetes no Azure – atualizar cluster | Microsoft Docs"
description: Tutorial do Kubernetes no Azure - atualizar cluster
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f203e01e6aaecf04944ee830df4f6adeb1c74d2f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Fazer upgrade do Kubernetes no AKS (Serviço de Contêiner do Azure)

Um cluster AKS (Serviço de Contêiner do Azure) pode ser atualizado usando a CLI do Azure. Durante o processo de upgrade, os nós Kubernetes são cuidadosamente [isolados e esvaziados](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) para minimizar as interrupções nos aplicativos em execução.

Neste tutorial, parte oito de oito, é feito o upgrade de um cluster Kubernetes. As tarefas a serem concluídas incluem:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. Em seguida, o aplicativo foi executado no cluster Kubernetes. 

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Obter versões de cluster

Antes de atualizar um cluster, use o comando `az aks get-versions` para verificar quais versões do Kubernetes estão disponíveis para upgrade.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Aqui você pode ver que a versão atual do nó é `1.7.7` e que a versão `1.8.1` está disponível.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades    AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  ----------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.1             1.7.7               1.8.1
```

## <a name="upgrade-cluster"></a>Fazer upgrade do cluster

Use o comando `az aks upgrade` para fazer upgrade dos nós de cluster. Os exemplos a seguir atualizam o cluster para a versão `1.8.1`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.1
```

Saída:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
  "name": "myK8sCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myK8sCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
    "kubernetesVersion": "1.8.1",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Validar o upgrade

Agora você pode confirmar se o upgrade obteve êxito com o comando `az aks show`.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.1                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez upgrade do Kubernetes em um cluster do AKS. Concluímos as seguintes tarefas:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

Siga este link para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Visão geral do AKS](./intro-kubernetes.md)