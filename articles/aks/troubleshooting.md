---
title: Solucionar problemas comuns do Serviço do Azure Kubernetes
description: Aprenda a solucionar problemas comuns ao usar o Serviço de Kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 8164e2db064523fe648ec9ef0c72754be846dff6
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327547"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Quando você cria ou gerencia clusters do Serviço de Kubernetes do Azure (AKS), você pode ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre como depurar problemas do Kubernetes?

Experimente o [guia oficial para solucionar problemas de clusters de Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também um [guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publicado por um engenheiro da Microsoft sobre solução de problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de “cota excedida” durante a criação ou atualização. O que devo fazer? 

Você precisa [solicitar núcleos](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para o AKS?

A configuração máxima de pods por nó é 30 por padrão, se você implantar um cluster AKS no portal do Azure.
A configuração máxima de pods por nó é 110 por padrão, se você implantar um cluster AKS na CLI do Azure. (Verifique se está usando a versão mais recente da CLI do Azure). Essa configuração padrão pode ser alterada usando o sinalizador `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo o erro “insuficienteSubnetSize” ao implantar um cluster AKS com a rede avançada. O que devo fazer?

Se o CNI (rede avançada) do Azure for usado, o AKS pré-aloca o endereço IP com base no máximo de pods por nó configurado. O número de nós em um cluster AKS pode estar em qualquer lugar entre 1 e 110. Com base no máximo de pods por nó, o tamanho da sub-rede deve ser maior que o "produto do número de nós e o máximo de pods por nó". A seguinte equação básica descreve isso:

Tamanho da sub-rede > número de nós no cluster (levando em consideração os futuros requisitos de escala) * máximo de pods por nó.

Para saber mais, confira [Planejar o endereçamento IP para o cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o pod trave nesse modo. Você pode examinar:

* O próprio pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl log <pod-name>`.

Para obter mais informações sobre como solucionar problemas de pod, consulte [Depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar RBAC em um cluster existente. Como posso fazer isso?

Infelizmente, a ativação do controle de acesso baseado em função (RBAC) em clusters existentes não é suportada no momento. Você deve explicitamente criar novos clusters. Se você usar a CLI, o RBAC é habilitado por padrão. Se você usar o portal do AKS, um botão de alternância para habilitar o RBAC está disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Eu criei um cluster com o RBAC habilitado usando a CLI do Azure com padrões ou o portal do Azure, agora vejo vários avisos no painel do Kubernetes. O painel costumava trabalhar sem nenhum aviso. O que devo fazer?

O motivo para os avisos no painel é que agora o cluster está habilitado com o RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é uma boa prática, já que a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser ativar o painel, siga os passos nesta [postagem de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo conectar-me ao painel. O que devo fazer?

A maneira mais fácil de acessar seu serviço fora do cluster é executar `kubectl proxy`, que enviará solicitações de proxy para sua porta local 8001 ao servidor Kubernetes API. A partir daí, o servidor de API pode usar um proxy ao seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se você não vir o painel do Kubernetes, verifique se o pod `kube-proxy` está em execução no namespace `kube-system`. Se não estiver em estado de execução, exclua o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter os logs, usando kubectl logs ou não é possível conectar-se ao servidor de API. Estou recebendo o "Erro do servidor: erro de discagem back-end: discar tcp..." O que devo fazer?

Certifique-se de que o grupo de segurança de rede (NSG) padrão não seja modificado e a porta 22 esteja aberta para conexão com o servidor da API. Verifique se o pod `tunnelfront` está em execução no namespace `kube-system`. Se não estiver, force a exclusão do pod e ele será reiniciado.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>Eu estou tentando atualizar ou dimensionar e estou recebendo erro "mensagem: Não é permitido alterar a propriedade 'imageReference'".  Como faço para corrigir esse problema?

Você pode estar recebendo este erro porque modificou as tags nos nós do agente dentro do cluster do AKS. Modificar e excluir tags e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos sob o grupo MC_ * no cluster do AKS quebra o objetivo de nível de serviço (SLO).
