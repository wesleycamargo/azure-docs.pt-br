---
title: Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 8aab091ed992a946cd78ecf4f0c8fdfff4185a08
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407545"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster do AKS só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.46 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Quando você cria um endereço IP público estático para ser usado com o AKS, o recurso de endereço IP precisa ser criado no grupo de recursos do **nó**. Obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o parâmetro de consulta `--query nodeResourceGroup`. O exemplo a seguir obtém o grupo de recursos do nó do nome do cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um endereço IP público estático com o comando [az network public ip create][az-network-public-ip-create]. Especifique o nome de grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso do endereço IP, como *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é mostrado, como na saída de exemplo reduzida a seguir:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
````

Mais tarde, você poderá obter o endereço IP público usando o comando [az network public-ip list][az-network-public-ip-list]. Especifique o nome do grupo de recursos do nó e, em seguida, confira o *ipAddress* como é mostrado no exemplo a seguir:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Para criar um serviço com o endereço IP público estático, adicione a propriedade `loadBalancerIP` e o valor do endereço IP público estático no manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>Solucionar problemas

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto do serviço de Kubernetes não existir ou não tiver sido criado no grupo de recursos de nó, a criação do serviço do balanceador de carga falhará. Para solucionar problemas, examine os eventos de criação do serviço com o comando [kubectl describe][kubectl-describe]. Forneça o nome do serviço, conforme especificado no manifesto do YAML, como é mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

As informações sobre o recurso do serviço de Kubernetes são exibidas. Os *Eventos* no final da saída de exemplo a seguir indicam que o *endereço IP fornecido pelo usuário não foi encontrado*. Nesses cenários, verifique se você criou o endereço IP público estático no grupo de recursos do nó e se o endereço IP especificado no manifesto do serviço de Kubernetes está correto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais controle sobre o tráfego de rede para seus aplicativos, convém [criar um controlador de entrada][aks-ingress-basic]. Você também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
