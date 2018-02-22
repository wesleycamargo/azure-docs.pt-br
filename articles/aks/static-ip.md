---
title: "Usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Contêiner do Azure)"
description: "Use um endereço IP estático com o balanceador de carga do AKS (Serviço de Contêiner do Azure)."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>Usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Contêiner do Azure)

Em alguns casos, como quando o balanceador de carga do AKS (Serviço de Contêiner do Azure) é recriado ou serviços Kubernetes com um tipo de balanceador de carga são recriados, o endereço IP público do serviço Kubernetes pode ser alterado. Este documento detalha como configurar um endereço IP estático para seus serviços Kubernetes.

## <a name="create-static-ip-address"></a>Criar endereço IP estático

Crie um endereço IP público estático para o serviço Kubernetes. O endereço IP deve ser criado no grupo de recursos que foi criado automaticamente durante a implantação do cluster. Para obter informações sobre os diferentes grupos de recursos de AKS e como identificar o grupo de recursos criado automaticamente, veja as [Perguntas frequentes do AKS][aks-faq-resource-group].

Use o comando [az network public ip create][az-network-public-ip-create] para criar o endereço IP.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Anote o endereço IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Se necessário, o endereço pode ser recuperado usando o comando [az network public-ip list][az-network-public-ip-list].

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Criar um serviço com o endereço IP

Depois que o endereço IP estático foi provisionado, um serviço Kubernetes pode ser criado com a propriedade `loadBalancerIP` e um valor do endereço IP estático.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>solução de problemas

Se o endereço IP estático não for criado ou for criado no grupo de recursos incorreto, a criação do serviço falhará. Para solucionar problemas, retorne eventos de criação de serviço com o comando [kubectl describe][kubectl-describe].

```console
$ kubectl describe service azure-vote-front

Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list