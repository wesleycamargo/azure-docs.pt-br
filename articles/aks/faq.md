---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure
description: Fornece respostas para algumas perguntas comuns sobre o Serviço de Kubernetes do Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: ea22b33233f85da117de54829e5a16bd7dcab36a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205241"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quais regiões do Azure fornecem o AKS (Serviço de Kubernetes do Azure) hoje?

Consulte a documentação [Regiões e disponibilidade][aks-regions] do Serviço de Kubernetes do Azure para obter uma lista completa.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

O Azure aplica automaticamente os patches de segurança aos nós no cluster em uma agenda noturna. No entanto, você é responsável por garantir que ps nós sejam reiniciados conforme necessário. Há várias opções para a execução de reinicializações de nó:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza automaticamente os [nós cordon e drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), em seguida, os traz de volta com a imagem mais recente do Ubuntu. Atualize a imagem do SO em seus nós sem alterar as versões do Kubernetes especificando a versão atual do cluster em `az aks upgrade`.
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização software livre para Kubernetes. O Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. Em seguida, ele combina reinicializações em todo o cluster, seguindo o mesmo processo de cordon e drain descrito anteriormente.

## <a name="does-aks-support-node-autoscaling"></a>O AKS é compatível com o dimensionamento automático de nó?

Sim, dimensionamento automática está disponível via [Kubernetes autoscaler][auto-scaler] como parte do Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>O AKS oferece suporte a controle de acesso baseado em função (RBAC) do Kubernetes?

Sim, RBAC pode ser habilitado ao [implantar um cluster AKS da CLI do Azure ou do modelo do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/aks/aad-integration). Esta função logo virá para o portal do Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Isto pode ser configurado?

AKS suporta os seguintes [controles de admissão][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Não é atualmente possível modificar a lista de controles de admissão no AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar AKS em minha rede virtual existente?

Sim, você consegue implantar um cluster AKS em uma rede virtual existente usando o [recurso de rede avançada](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Posso restringir o servidor de API do Kubernetes para somente estar acessível dentro de minha rede virtual?

Não no momento. O servidor de API do Kubernetes é exposto como um FQDN (nome de domínio totalmente qualificado) público. Você deve controlar o acesso ao seu cluster usando [RBAC (controle de acesso baseado em função) do Kubernetes e ADD (Azure Active Directory)](https://docs.microsoft.com/en-us/azure/aks/aad-integration).

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

AKS não é nativamente integrado com o Azure Key Vault neste momento. Entretanto, há soluções de comunidade como [o  acs-keyvault-agent do Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Para executar contêineres do Windows Server, você precisa executar nós baseados no Windows Server. Nós baseados em Windows Server não estão disponíveis no AKS neste momento. É possível, no entanto, usar o Kubelet Virtual para agendar contêineres do Windows em Instâncias de Contêiner do Azure e gerenciá-los como parte do seu cluster do AKS. Para obter mais informações, consulte [Use Virtual Kubelet with AKS][virtual-kubelet] (Usar Virtual Kubelet com o AKS).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

Cada implantação AKS abrange dois grupos de recursos. O primeiro é criado por você e contém somente o recurso de serviço Kubernetes. O provedor de recursos AKS cria automaticamente um segundo durante a implantação com um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O segundo grupo de recursos contém todos os recursos de infraestrutura associados ao cluster, como VMs, rede e armazenamento. Ele é criado para simplificar a limpeza de recursos.

Se você estiver criando recursos que serão usados com o cluster AKS, como contas de armazenamento ou o endereço IP público reservado, você deve colocá-los no grupo de recursos gerados automaticamente.

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Em um SLA (Contrato de Nível de Serviço), o provedor concorda em reembolsar o cliente pelo custo do serviço caso o nível de serviço publicado não seja atendido. Como o AKS é gratuito, não há nenhum custo a reembolsar e, portanto, nenhum SLA formal. No entanto, procuramos manter uma disponibilidade de pelo menos 99,5% para o servidor da API do Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
