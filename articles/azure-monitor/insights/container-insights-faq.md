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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418335"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Monitor para contêineres
Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para contêineres. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Por que não vejo dados se o workspace do Log Analytics está configurado com o tipo de preço Gratuito? 

Você pode ter alcançado o limite padrão de 500 MB ou especificado um limite diário para controlar a quantidade de dados a serem coletados diariamente. Para verificar e gerenciar o uso de dados, confira [Custo e uso de dados de log](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Quais são os estados de contêineres especificados na tabela ContainerInventory?
A tabela ContainerInventory contém informações sobre contêineres parados e em execução. A tabela é preenchida por um fluxo de trabalho dentro do agente que consulta o docker por todos os contêineres (em execução e parados) e encaminha esses dados ao workspace do Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Como eu resolvo erros relacionados à **Registro de assinatura ausente para Microsoft.OperationsManagement**?
Para resolver o erro, registre o provedor de recursos **Microsoft.OperationsManagement** na assinatura em que o workspace é definido. A documentação para saber como fazer isso pode ser encontrada [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>O Azure Monitor para contêineres inclui suporte para clusters AKS habilitados para RBAC?
Clusters AKS habilitados para RBAC atualmente não são compatíveis com a solução. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados desses clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como habilito a coleta de log para contêineres no namespace kube-system por meio do Helm?
A coleta de log de contêineres no namespace kube-system está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para saber mais, confira a página do GitHub [Azure Monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers). 

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

Esses dados serão parecidos com o seguinte no Azure Monitor para logs ao consultá-los:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para detalhes sobre esse problema, examine o seguinte [link do github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Como resolvo erros do Azure Active Directory ao habilitar logs dinâmicos? 
Você poderá ver o seguinte erro: **A URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: '60b4dec7-5a69-4165-a211-12c40b5c0435'**. A correção para isso pode ser encontrada no artigo [Como exibir logs de contêiner em tempo real com o Azure Monitor para contêineres](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Próximas etapas
Para começar a monitorar seu cluster do AKS, veja [Como integrar o Azure Monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento. 