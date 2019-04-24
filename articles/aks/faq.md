---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)
description: Fornece respostas para algumas das perguntas mais comuns sobre o Serviço de Kubernetes do Azure (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: ae92a5c894b186a1c8b471c1b446a88299742aec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466368"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quais regiões do Azure fornecem o AKS (Serviço de Kubernetes do Azure) hoje?

Para obter uma lista completa das regiões disponíveis, consulte [AKS regiões e disponibilidade][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>O AKS é compatível com o dimensionamento automático de nó?

Sim, dimensionamento automática está disponível via [Kubernetes autoscaler][auto-scaler] como parte do Kubernetes 1.10. Para obter mais informações sobre como configurar e usar o dimensionador automático de cluster, consulte [dimensionamento automático de Cluster no AKS][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>O AKS oferece suporte a controle de acesso baseado em função (RBAC) do Kubernetes?

Sim, o Kubernetes RBAC é ativado por padrão quando os clusters são criados com a CLI do Azure. O RBAC pode ser habilitado para clusters criados usando o portal ou modelos do Azure.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar AKS em minha rede virtual existente?

Sim, você pode implantar um cluster do AKS em uma rede virtual existente usando o [recurso de rede avançado][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Posso restringir o servidor de API do Kubernetes para somente estar acessível dentro de minha rede virtual?

Não no momento. O servidor de API do Kubernetes é exposto como um FQDN (nome de domínio totalmente qualificado) público. Você pode controlar o acesso ao seu cluster usando [Kubernetes com base em função de controle de acesso (RBAC) e Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

Sim, o Azure aplica automaticamente os patches de segurança para os nós no cluster em uma agenda noturna. No entanto, você é responsável por garantir que ps nós sejam reiniciados conforme necessário. Há várias opções para a execução de reinicializações de nó:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza automaticamente [cordon e drain nodes][cordon-drain], depois traz de volta cada nó com a última imagem do Ubuntu e uma nova versão de patch ou uma versão menor do Kubernetes. Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização software livre para Kubernetes. O Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. As reinicializações do SO são gerenciadas no cluster usando o mesmo [cordon e o processo de drenagem][cordon-drain] como uma atualização do cluster.

Para obter mais informações sobre como usar o kured, consulte [Aplicar atualizações de segurança e o kernel para nós do AKS][node-updates-kured].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

Cada implementação do AKS abrange dois grupos de recursos:

- O primeiro grupo de recursos é criado por você e contém apenas o recurso de serviço do Kubernetes. O provedor de recursos do AKS cria automaticamente o segundo durante a implantação, como *MC_myResourceGroup_myAKSCluster_eastus*.
- Este segundo recurso de grupo, como *MC_myResourceGroup_myAKSCluster_eastus*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem as máquinas virtuais do nó do Kubernetes, rede virtual e armazenamento. Esse grupo de recursos separado é criado para simplificar a limpeza de recursos.

Se você criar recursos para uso com seu cluster AKS, como contas de armazenamento ou endereços IP públicos reservados, coloque-os no grupo de recursos gerado automaticamente.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Posso modificar tags e outras propriedades dos recursos do AKS no grupo de recursos MC_ *?

Modificar e excluir as marcas criadas pelo Azure e outras propriedades de recursos no grupo de recursos *MC_** pode levar a resultados inesperados, além de erros de atualização e dimensionamento. Há suporte para criar e modificar marcas personalizadas adicionais, como atribuir uma unidade de negócios ou um centro de custo. Modificar os recursos sob *MC_** no cluster AKS interrompe o objetivo no nível do serviço (SLO). Para obter mais informações, confira [O AKS oferece contrato no nível do serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Controladores de admissão podem ser adicionados ou removidos?

AKS suporta os seguintes [controles de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Não é atualmente possível modificar a lista de controles de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

Atualmente, o AKS não está integrado de maneira nativa ao Cofre de Chaves do Azure. No entanto, o [FlexVolume de Cofre de chave do Azure para o projeto Kubernetes][keyvault-flexvolume] permite direcionar a integração de pods Kubernetes para os segredos do Cofre de chaves.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Para executar contêineres do Windows Server, você precisa executar nós baseados no Windows Server. Nós baseados em Windows Server não estão disponíveis no AKS neste momento. É possível, no entanto, usar o Kubelet Virtual para agendar contêineres do Windows em Instâncias de Contêiner do Azure e gerenciá-los como parte do seu cluster do AKS. Para obter mais informações, consulte [Use Virtual Kubelet with AKS][virtual-kubelet] (Usar Virtual Kubelet com o AKS).

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Em um contrato de nível serviço (SLA), o provedor concorda em reembolse o cliente para o custo do serviço se o nível de serviço publicado não for atendido. Como o AKS é gratuito, não há nenhum custo a reembolsar e, portanto, nenhum SLA formal. No entanto, a AKS procura manter a disponibilidade de pelo menos 99,5% para o servidor da API do Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

