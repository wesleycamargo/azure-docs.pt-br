---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: f6b8e964f4277150e104cd6d77db092aaa8553b4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

O Serviço de Kubernetes do Azure (AKS) facilita a execução de tarefas comuns de gerenciamento, incluindo o upgrade de clusters Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Antes de atualizar um cluster, use o comando `az aks get-upgrades` para verificar quais versões do Kubernetes estão disponíveis para upgrade.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Saída:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Temos três versões disponíveis para upgrade: 1.9.1, 1.9.2 e 1.9.6. Podemos usar o comando `az aks upgrade` para atualizar para a versão mais recente disponível.  Durante o processo de upgrade, os nós são cuidadosamente [isolados e esvaziados][kubernetes-drain] para minimizar as interrupções nos aplicativos em execução.  Antes de iniciar uma atualização de cluster, certifique-se de que você tenha capacidade adicional de computação suficiente para lidar com a carga de trabalho, à medida que nós de cluster forem adicionados e removidos.

> [!NOTE]
> Ao atualizar um cluster do AKS, as versões secundárias do Kubernetes não poderão ser ignoradas. Por exemplo, upgrades entre 1.7.x > 1.8.x ou 1.8.x > 1.9.x são permitidos, porém 1.7 > 1.9 não é.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
```

Saída:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
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
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.9.6",
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

Confirme se o upgrade teve êxito com o comando `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais do AKS.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md