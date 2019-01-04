---
title: Solucionar problemas comuns do Serviço do Azure Kubernetes
description: Aprenda a solucionar problemas comuns ao usar o Serviço de Kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384098"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS
Quando você cria ou gerencia clusters do AKS, ocasionalmente, você pode encontrar problemas. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>Em geral, onde encontro informações sobre como depurar problemas do Kubernetes?

[Este](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) é um link oficial para solucionar problemas de clusters de kubernetes.
[Aqui](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) é um link para um guia de solução de problemas publicado por um engenheiro da Microsoft sobre pods de solução de problemas, nós, clusters etc.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de cota excedida durante a criação ou atualização. O que devo fazer? 

Você precisará solicitar os núcleos [aqui](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para o AKS?

Os pods máximos por nó são definidos como 30 por padrão se você implantar um cluster AKS no portal do Azure.
Os pods máximos por nó são definidos como 110 por padrão, se você implantar um cluster AKS na CLI do Azure. (Verifique se você está usando a versão mais recente da CLI do Azure). Essa configuração padrão pode ser alterada usando o sinalizador –max-nodes-per-pod no comando az aks create.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo o erro “insuficienteSubnetSize” ao implantar um cluster AKS com a rede avançada. O que devo fazer?

Na opção Personalizada VNET selecionada para rede durante as criações do AKS, a CNI do Azure é usada para o IPAM. O número de nós em um cluster AKS pode estar em qualquer lugar entre 1 e 100. Com base em 2) acima, o tamanho da sub-rede deve ser maior que o produto do número de nós e o máximo do pod por nó Tamanho da sub-rede> não de nós no cluster * pods máx. por nó.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo "CrashLoopBackOff". O que devo fazer?

Pode haver vários motivos para o pod trave nesse modo. Você pode querer olhar para o 
* O próprio pod usando `kubectl describe pod <pod-name>`
* Os logs de uso  `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Estou tentando habilitar RBAC em um cluster existente. Você pode me dizer como posso fazer isso?

Infelizmente, a ativação do RBAC em clusters existentes não é suportada no momento. Você precisará explicitamente criar novos clusters. Se você usar o CLI, o RBAC será ativado por padrão, enquanto um botão de alternância para ativá-lo estará disponível no fluxo de criação do portal do AKS.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Eu criei um cluster usando a CLI do Azure com padrões ou o portal do Azure com RBAC ativado e vários avisos no painel do kubernetes. O painel usado para trabalhar antes sem nenhum aviso. O que devo fazer?

O motivo para receber avisos no painel é que agora ele está habilitado com o RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é considerada uma boa prática, já que a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser ativar o painel, siga este [blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) para ativá-lo.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Não consigo conectar-se ao painel. O que devo fazer?

A maneira mais fácil de acessar seu serviço fora do cluster é executar o proxy kubectl, que enviará solicitações de proxy para sua porta local 8001 para o servidor Kubernetes API. A partir daí, o apiserver pode fazer proxy para o seu serviço: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#! / Node? Namespace = default

Se você não vir o painel do kubernetes, verifique se o pod do kube-proxy está sendo executado no namespace do Kube-system. Se não estiver em execução, exclua o pod e ele será reiniciado.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consegui obter logs usando os logs do Kubectl ou não consigo conectar-me ao servidor da API, obtendo o “Erro do servidor: backend de discagem de erro: discar tcp…”. O que devo fazer?

Certifique-se de que o NSG padrão não seja modificado e a porta 22 esteja aberta para conexão com o servidor da API. Verifique se o pod de fachada de túnel está sendo executado no namespace do sistema kube. Se não for, force excluí-lo e ele será reiniciado.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>Eu estou tentando atualizar ou dimensionar e estou recebendo "mensagem": "Não é permitido alterar a propriedade 'imageReference'". Erro.  Como faço para corrigir esse problema?

É possível que você esteja recebendo este erro porque modificou as tags nos nós do agente dentro do cluster do AKS. Modificar e excluir tags e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos sob o MC_ * no cluster do AKS quebra o SLO.

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Como faço para renovar o segredo da entidade de serviço em meu cluster do AKS?

Por padrão, os clusters do AKS são criados com uma entidade de serviço que tem um tempo de término de um ano. Conforme você se aproxima da data do término de um ano, pode redefinir as credenciais para estender a entidade de serviço por um período adicional.

Este exemplo executa as seguintes etapas:

1. Obtém a ID de entidade de serviço do seu cluster usando o comando [az aks show](/cli/azure/aks#az-aks-show).
1. Lista o segredo do cliente da entidade de serviço usando [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list)
1. Estende a entidade de serviço por mais um ano usando o comando [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). O segredo do cliente da entidade de serviço deve permanecer o mesmo para o cluster do AKS ser executado corretamente.

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
