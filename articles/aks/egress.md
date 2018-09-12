---
title: Tráfego de saída da lista de permissões do cluster do AKS (Serviço do Kubernetes do Azure)
description: Tráfego de saída da lista de permissões de um cluster do AKS (Serviço do Kubernetes do Azure)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347793"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Saída do AKS (Serviço do Kubernetes do Azure)

Por padrão, o endereço de saída de um cluster do Serviço do Kubernetes do Azure (AKS) é atribuído aleatoriamente. Essa configuração não é ideal quando é necessário identificar um endereço IP para acessar serviços externos. Este documento fornece detalhes de como criar e manter um endereço IP de saída atribuído estaticamente em um cluster AKS.

## <a name="egress-overview"></a>Visão geral de saída

O tráfego de saída de um cluster AKS segue as convenções do Azure Load Balancer, documentadas [aqui][outbound-connections]. Até o primeiro serviço Kubernetes do tipo `LoadBalancer` ser criado, os nós do agente não fazem parte de qualquer pool do Azure Load Balancer. Nessa configuração, os nós não têm um endereço IP público de nível de instância. O Azure converte o fluxo de saída para um endereço IP de origem pública que não é configurável ou determinístico.

Após o serviço Kubernetes do tipo `LoadBalancer` ser criado, os nós do agente são adicionados a um pool do Azure Load Balancer. Para o fluxo de saída, o Azure o converte para o primeiro endereço IP público configurado no balanceador de carga.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Para impedir o uso de endereços IP aleatórios, crie um endereço IP estático e certifique-se de que o balanceador de carga use este endereço. O endereço IP deve ser criado no grupo de recursos do **nó** do AKS.

Obtenha o nome do grupo de recursos com o comando [az resource show][az-resource-show]. Atualize o nome do grupo de recursos e o nome do cluster para corresponder ao seu ambiente.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Em seguida, use o comando [az network public-ip create][public-ip-create] para criar um endereço IP público estático. Atualize o nome do grupo de recursos para coincidir com o nome obtido na etapa anterior.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Agora que você tem um endereço IP, crie um serviço Kubernetes com o tipo `LoadBalancer` e atribua o endereço IP ao serviço.

Crie um arquivo chamado `egress-service.yaml` e copie no YAML a seguir. Atualize o endereço IP para coincidir com seu ambiente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

A criação desse serviço configura um novo IP de front-end no Azure Load Balancer. Se você não tiver nenhum outro IP configurado, **todo** tráfego de saída deverá usar esse endereço. Quando vários endereços são configurados no Azure Load Balancer, a saída usa o primeiro IP desse balanceador de carga.

## <a name="verify-egress-address"></a>Verificar o endereço de saída

Para verificar se o endereço IP público está sendo usado, use um serviço como `checkip.dyndns.org`.

Inicie e anexe a um pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Se necessário, instale curl no contêiner:

```console
$ apt-get update && apt-get install curl -y
```

`checkip.dyndns.org` Curl, que retorna o endereço IP de saída:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Veja se o endereço IP corresponde ao endereço IP estático anexado ao balanceador de carga do Azure.

## <a name="ingress-controller"></a>Controlador de entrada

Para evitar que vários endereços IP públicos sejam mantidos no Azure Load Balancer, considere o uso de um controlador de entrada. Os controladores de entrada oferecem benefícios como balanceamento de carga, encerramento de SSL/TLS, suporte para regravações de URI e criptografia upstream SSL/TLS. Veja mais informações sobre os controladores de entrada no AKS no guia [Configurar o controlador de entrada NGINX em um cluster AKS][ingress-aks-cluster].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o software demonstrado neste documento.

- [CLI do Helm][helm-cli-install]
- [Controlador de entrada NGINX ][nginx-ingress]
- [Conexões de saída do Azure Load Balancer][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
