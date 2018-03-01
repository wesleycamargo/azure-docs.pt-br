---
title: "Perguntas frequentes sobre o Serviço de Contêiner do Azure"
description: "Fornece respostas para algumas perguntas comuns sobre o Serviço de Contêiner do Azure."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 59dceded1e72e6e0e3d1a2bb25ca63bd023a9d21
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Perguntas frequentes sobre o AKS (Serviço de Contêiner do Azure)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Contêiner do Azure).

## <a name="which-azure-regions-provide-the-azure-container-service-aks-today"></a>Quais regiões do Azure fornecem o AKS (Serviço de Contêiner do Azure) hoje?

- Canadá Central 
- Leste do Canadá 
- Centro dos EUA 
- Leste dos EUA 
- Sudeste da Ásia 
- Europa Ocidental 
- Oeste dos EUA 2 

## <a name="when-will-additional-regions-be-added"></a>Quando outras regiões serão adicionadas? 

Outras regiões são adicionadas de acordo com o aumento de demanda.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS? 

O Azure aplica automaticamente os patches de segurança aos nós no cluster em uma agenda noturna. No entanto, você é responsável por garantir que ps nós sejam reiniciados conforme necessário. Há várias opções para a execução de reinicializações de nó:

- Manualmente, por meio do portal do Azure ou da CLI do Azure. 
- Atualizando o cluster AKS. O cluster atualiza automaticamente os [nós cordon e drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), em seguida, os traz de volta com a imagem mais recente do Ubuntu. Você pode atualizar a imagem do SO em seus nós sem alterar as versões do Kubernetes especificando a versão atual do cluster em `az aks upgrade`.
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização software livre para Kubernetes. O Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. Em seguida, ele combina essas reinicializações em todo o cluster, seguindo o mesmo processo de cordon e drain descrito anteriormente.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>É recomendável que os clientes usem ACS ou AKS? 

Enquanto AKS permanece na versão prévia, é recomendável criar clusters de produção usando Kubernetes ACS ou [acs-engine](https://github.com/azure/acs-engine). Você pode usar AKS para implantações de prova de conceito e ambientes de desenvolvimento/teste.

## <a name="when-will-acs-be-deprecated"></a>Quando o ACS será preterido? 

O ACS será preterido próximo do período em que o AKS entrará em GA. Você terá 12 meses, após essa data, para migrar os clusters para o AKS. Durante o período de 12 meses, você pode executar todas as operações do ACS.

## <a name="does-aks-support-node-autoscaling"></a>O AKS é compatível com o dimensionamento automático de nó? 

O dimensionamento automático de nó não é compatível, mas está previsto. Essa [implementação de dimensionamento automático][auto-scaler] de software livre pode ser interessante para você.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>O AKS oferece suporte a controle de acesso baseado em função (RBAC) do Kubernetes?

Não, o RBAC não há suporte atualmente para AKS, mas estará disponível em breve.   

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar AKS em minha rede virtual existente?

Não, isso ainda não está disponível, mas estará disponível em breve.

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS? 

Não, mas essa integração está planejada. Enquanto isso, você pode testar essa solução da [Hexadite][hexadite]. 

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Não, o AKS atualmente não fornece nós de agente baseados no Windows Server, portanto você não pode executar contêineres do Windows Server. Se você precisar executar contêineres do Windows Server em Kubernetes no Azure, consulte a [documentação para acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS? 

Cada implantação AKS abrange dois grupos de recursos. O primeiro é criado por você e contém somente o recurso AKS. O provedor de recursos AKS cria automaticamente um segundo durante a implantação com um nome como *MC_myResourceGRoup_myAKSCluster_eastus*. O segundo grupo de recursos contém todos os recursos de infraestrutura associados ao cluster, como VMs, rede e armazenamento. Ele é criado para simplificar a limpeza de recursos. 

Se você estiver criando recursos que serão usados com o cluster AKS, como contas de armazenamento ou o endereço IP público reservado, você deve colocá-los no grupo de recursos gerados automaticamente.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  