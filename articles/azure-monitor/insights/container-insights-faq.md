---
title: Perguntas frequentes sobre o Azure Monitor para contêineres | Microsoft Docs
description: O Azure Monitor para contêineres é uma solução que monitora a integridade dos clusters AKS e Instâncias de Contêiner no Azure. Este artigo responde a perguntas comuns.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494654"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Monitor para contêineres

Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para contêineres. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados no meu espaço de trabalho do Log Analytics?

Se não for possível ver os dados no espaço de trabalho do Log Analytics em um determinado horário todos os dias, você talvez tenha atingido o limite de 500 MB padrão ou o limite diário especificado para controlar a quantidade de dados a serem coletados diariamente. Quando o limite for atingido para o dia, o conjunto de dados será interrompida e reiniciada somente no dia seguinte. Para examinar o uso de dados e atualizar para um tipo de preço diferente com base nos seus padrões de uso antecipado, consulte [custo e uso de dados de Log](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os estados de contêiner especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres parados e em execução. A tabela é preenchida por um fluxo de trabalho dentro do agente que consulta o docker por todos os contêineres (em execução e parados) e encaminha esses dados ao espaço de trabalho do Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como resolver **registro de assinatura ausente** erro?

Se você receber o erro **registro de assinatura ausente para Microsoft.OperationsManagement**, você pode resolvê-lo ao registrar o provedor de recursos **Microsoft.OperationsManagement** no assinatura onde o espaço de trabalho é definido. A documentação para saber como fazer isso pode ser encontrada [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para o RBAC habilitado clusters AKS?

A solução de monitoramento de contêiner não dá suporte a RBAC, mas ele é compatível com o Azure Monitor para contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados desses clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como habilito a coleta de log para contêineres no namespace kube-system por meio do Helm?

A coleta de log de contêineres no namespace kube-system está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para obter mais informações, consulte o [do Azure Monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) página do GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como posso atualizar o omsagent para a versão mais recente?

Para saber como atualizar o agente, confira [Gerenciamento de agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Como habilito o registro em log de várias linhas?

No momento o Azure Monitor para contêineres não é compatível com registro em log de várias linhas, mas há soluções alternativas disponíveis. Você pode configurar todos os serviços para gravar em formato JSON e, em seguida, o Docker/Moby vai gravá-los como uma única linha.

Por exemplo, você pode encapsular seu log como um objeto JSON, conforme mostrado no exemplo de um aplicativo do Node.js abaixo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados se parecerá com o exemplo a seguir no Azure Monitor para logs quando você consulta para ele:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para detalhes sobre esse problema, examine o seguinte [link do github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como resolver erros de AD do Azure quando habilitar logs dinâmicos? 

Você poderá ver o seguinte erro: **A resposta à url especificada na solicitação não coincide com as urls de resposta configuradas para o aplicativo: ' < ID do aplicativo\>'**. A solução para resolvê-lo pode ser encontrada no artigo [como exibir contêiner logs em tempo real com o Azure Monitor para contêineres](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não é possível atualizar cluster após a integração?

Se depois de habilitar o Azure Monitor para contêineres para um cluster do AKS, você excluir o espaço de trabalho do Log Analytics no cluster estava enviando seus dados, ao tentar atualizar o cluster falhará. Para resolver esse problema, você precisará desabilitar o monitoramento e reabilitá-la fazendo referência a outro espaço de trabalho válido na sua assinatura. Quando você tenta executar a atualização do cluster novamente, ele deve processar e concluir com êxito.  

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar seu cluster do AKS, veja [Como integrar o Azure Monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento. 