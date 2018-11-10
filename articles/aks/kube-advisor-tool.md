---
title: Verifique suas implantações do Kubernetes no Azure para implementar as melhores práticas
description: Verifique suas implantações do Kubernetes no Azure para implementar as melhores práticas
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 01095ac4ed8e362f1a89a53b10b5da6a547feb57
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218621"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verificar as práticas recomendadas do Kubernetes no seu cluster

Existem várias práticas recomendadas que você deve seguir nas implantações do Kubernetes para garantir o melhor desempenho e resiliência dos seus aplicativos. Você pode usar a ferramenta kube-advisor para procurar implantações que não estejam seguindo essas sugestões.

## <a name="about-kube-advisor"></a>Sobre o Supervisor de kube

A ferramenta [kube-advisor][kube-advisor-github] é um único contêiner projetado para ser executado em seu cluster. Ele consulta o servidor da API do Kubernetes para obter informações sobre suas implementações e retorna um conjunto de melhorias sugeridas.

> [!NOTE]
> A ferramenta kube-advisor é suportada pela Microsoft com base no melhor esforço. Questões e sugestões devem ser arquivadas no GitHub.

## <a name="running-kube-advisor"></a>Executando o Supervisor de kube

Para executar a ferramenta em um cluster configurado para [controle de acesso baseado em função (RBAC)](aad-integration.md), use os seguintes comandos. O primeiro comando cria uma conta de serviço do Kubernetes. O segundo comando executa a ferramenta em um pod usando essa conta de serviço e configura o pod para exclusão após sua saída. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Se você não estiver usando o RBAC, poderá executar o comando da seguinte maneira:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Em alguns segundos, você verá uma tabela descrevendo os possíveis aprimoramentos em suas implantações.

![Saída do Kube-Advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Verificações executadas

A ferramenta valida várias práticas recomendadas do Kubernetes, cada uma com sua própria remediação sugerida.

### <a name="resource-requests-and-limits"></a>Solicitações de recursos e limites

O Kubernetes suporta a definição de [solicitações de recursos e limites nas especificações do pod ][kube-cpumem]. A solicitação define o mínimo de CPU e memória necessária para executar o contêiner. O limite define a CPU e a memória máximas que devem ser permitidas.

Por padrão, nenhuma solicitação ou limite é definido nas especificações do pod. Isso pode levar a que os nós sejam excedidos em excesso e que os recipientes fiquem sem alimentação. A ferramenta kube-advisor destaca os pods sem solicitações e limites definidos.

## <a name="cleaning-up"></a>Limpando

Se o seu cluster tiver o RBAC ativado, você poderá limpar o `ClusterRoleBinding` depois de executar a ferramenta usando o seguinte comando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se você estiver executando a ferramenta em um cluster que não esteja habilitado para RBAC, nenhuma limpeza será necessária.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas com o serviço Kubernetes do Azure](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor