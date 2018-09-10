---
title: Criar um balanceador de carga interno do AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar e usar um balanceador de carga interno para expor seus serviços com o AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001388"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure)

O balanceamento de carga interno torna um serviço do Kubernetes acessível a aplicativos executando na mesma rede virtual que o cluster do Kubernetes. Este artigo mostra como criar e usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure). O Azure Load Balancer está disponível em dois SKUs: Básico e Standard. AKS usa o SKU Básico.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um balanceador de carga interno, criar um manifesto do serviço com o tipo de serviço *LoadBalancer* e a anotação *azure-load-balancer-internal*, conforme mostrado no exemplo a seguir:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Depois de implantado com o `kubetctl apply`, um balanceador de carga do Azure é criado e disponibilizado na mesma rede virtual que o cluster do AKS.

![Imagem do balanceador de carga interno do AKS](media/internal-lb/internal-lb.png)

Quando você exibe os detalhes do serviço, o endereço IP na coluna *EXTERNAL-IP* é o endereço IP do balanceador de carga interno, conforme mostrado no exemplo a seguir:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se desejar usar um endereço IP específico com o balanceador de carga interno, adicione a propriedade *loadBalancerIP* às especificações do balanceador de carga. O endereço IP especificado deve residir na mesma sub-rede que o cluster do AKS e não deve ser atribuído a um recurso.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Quando você exibe os detalhes do serviço, o endereço IP no *EXTERNAL-IP* reflete o endereço IP especificado:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Usar redes privadas

Quando você cria seu cluster do AKS, é possível especificar as configurações de rede avançadas. Essa abordagem permite implantar o cluster na rede virtual e nas sub-redes existentes do Azure. Um cenário é implantar o cluster do AKS em uma rede privada conectada ao seu ambiente local e executar serviços acessíveis apenas internamente. Para obter mais informações, consulte [configuração de rede avançada no AKS][advanced-networking].

Não é necessário realizar nenhuma alteração nas etapas anteriores para implantar um balanceador de carga interno em um cluster do AKS que usa uma rede privada. O balanceador de carga é criado no mesmo grupo de recursos que seu cluster do AKS, mas conectado à sua rede virtual e à sub-rede privadas, conforme mostrado no exemplo a seguir:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Talvez seja necessário conceder à entidade de serviço do seu cluster do AKS a função *Colaborador de rede* para o grupo de recursos no qual seus recursos de rede virtual do Azure são implantados. Exiba a entidade de serviço com [az aks show][az-aks-show], como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Para criar uma atribuição de função, use o comando [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Especificar uma sub-rede diferente

Para especificar uma sub-rede para o balanceador de carga, adicione a anotação *azure-load-balancer-internal-subnet* ao seu serviço. A sub-rede especificada deve estar na mesma rede virtual que seu cluster do AKS. Quando implantado, endereço *EXTERNAL-IP* do balanceador de carga faz parte da sub-rede especificada.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>Excluir o balanceador de carga

Quando todos os serviços que usam o balanceador de carga interno são excluídos, o próprio balanceador de carga também é excluído.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços do Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create