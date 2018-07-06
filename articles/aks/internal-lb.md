---
title: Criar um balanceador de carga interno do AKS (Serviço de Kubernetes do Azure)
description: Usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097913"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure)

O balanceamento de carga interno torna um serviço do Kubernetes acessível a aplicativos executando na mesma rede virtual que o cluster do Kubernetes. Este documento detalha a criação de um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure). O Azure Load Balancer está disponível em dois SKUs: Básico e Standard. AKS usa o SKU Básico.

## <a name="create-internal-load-balancer"></a>Criar balanceador de carga interno

Para criar um balanceador de carga interno, compile um manifesto de serviço com o tipo de serviço `LoadBalancer` e a anotação`azure-load-balancer-internal` conforme visto na amostra a seguir.

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

Depois de implantado, um balanceador de carga do Azure é criado e disponibilizado na mesma rede virtual que o cluster do AKS.

![Imagem do balanceador de carga interno do AKS](media/internal-lb/internal-lb.png)

Ao recuperar os detalhes do serviço, o endereço IP na coluna `EXTERNAL-IP` é o endereço IP do balanceador de carga interno.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se você quiser usar um endereço IP específico com o balanceador de carga interno, adicione a propriedade `loadBalancerIP` às especificações do balanceador de carga. O endereço IP especificado deve residir na mesma sub-rede que o cluster do AKS e não deve ser atribuído a um recurso.

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

Ao recuperar os detalhes do serviço, o endereço IP em `EXTERNAL-IP` deve refletir o endereço IP especificado.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Excluir o balanceador de carga

Quando todos os serviços que usam o balanceador de carga interno tiverem sido excluídos, o próprio balanceador de carga também será excluído.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços do Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
