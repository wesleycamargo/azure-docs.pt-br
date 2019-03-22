---
title: Logs de análise de eventos do Azure do Service Fabric com o Azure Monitor | Microsoft Docs
description: Saiba mais sobre como visualizar e analisar eventos usando os logs do Azure Monitor para monitoramento e diagnóstico de clusters do Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 2f3106b33ab0cbea95efe2ac42c05a8543719190
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246909"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Visualização e análise de eventos com os logs do Azure Monitor
 Os logs do Azure Monitor coleta e analisa a telemetria de aplicativos e serviços hospedados na nuvem e fornece ferramentas de análise para ajudá-lo a maximizar a disponibilidade e desempenho. Este artigo descreve como executar consultas nos logs do Azure Monitor para obter insights e solucionar problemas que está acontecendo em seu cluster. As seguintes perguntas comuns são abordadas:

* Como solucionar problemas de eventos de integridade?
* Como saber quando um nó fica inoperante?
* Como saber se os serviços do meu aplicativo foram iniciados ou interrompidos?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Visão geral do espaço de trabalho do Log Analytics

>[!NOTE] 
>Enquanto o armazenamento de diagnóstico é habilitado por padrão no momento da criação do cluster, você deve ainda configurar o espaço de trabalho do Log Analytics para ler do armazenamento de diagnóstico.

O Azure Monitor registra dados de coleta de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. O Azure Monitor registra dá suporte a eventos, dados de desempenho ou outros dados personalizados. Fazer check-out [as etapas para configurar a extensão de diagnóstico para eventos agregados](service-fabric-diagnostics-event-aggregation-wad.md) e [etapas para criar um espaço de trabalho do Log Analytics para ler os eventos no armazenamento](service-fabric-diagnostics-oms-setup.md) para garantir que os dados estão fluindo para o Azure Monitor registra em log.

Depois que dados são recebidos pelos logs do Azure Monitor, o Azure tem várias *soluções de monitoramento* que são soluções pré-empacotados ou painéis operacionais para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Este artigo descreve como usar a solução de Análise do Service Fabric, que é criada com o workspace.

## <a name="access-the-service-fabric-analytics-solution"></a>Acesse a solução de Análise do Service Fabric

No [Portal do Azure](https://portal.azure.com), vá para o grupo de recursos em que você criou a solução de análise do Service Fabric.

Selecione o recurso **ServiceFabric\<nomeDoEspaçoDeTrabalhoOMS\>**.

No `Summary`, você verá o bloco na forma de um gráfico para cada uma das soluções habilitadas, incluindo uma para o Service Fabric. Clique no grafo **Service Fabric** para ir para a solução de Análise do Service Fabric.

![Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

A imagem a seguir mostra a home page da solução de análise do Service Fabric. Essa página fornece uma exibição de instantâneo do que está acontecendo em seu cluster.

![Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se você habilitou o diagnóstico após a criação do cluster, você pode ver eventos para 

* [Eventos de cluster do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos do Service Fabric prontos para uso, é possível coletar eventos do sistema mais detalhados ao [atualizar a configuração da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Exibir eventos de malha de serviço, incluindo ações em nós

Na página Análise do Service Fabric, clique no grafo para **Eventos do Service Fabric**.

![Canal Operacional de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Clique em **Lista** para exibir os eventos em uma lista. Uma vez aqui, você verá todos os eventos do sistema que foram coletados. Para referência, esses são do **WADServiceFabricSystemEventsTable** no armazenamento do Azure conta e da mesma forma os eventos de serviços e atores confiáveis, você vê ao lado são dessas respectivas tabelas.
    
![Canal Operacional de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Como alternativa, você pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para localizar o que você está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As IDs de evento usadas abaixo são encontradas em [referência de eventos do canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Você pode consultar em vários outros campos como em nós específicos (computador), o serviço do sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Exibir eventos dos Atores e Serviços Confiáveis do Service Fabric

Na página Análise do Service Fabric, clique no grafo para o **Reliable Services**.

![Reliable Services de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Clique em **Lista** para exibir os eventos em uma lista. Aqui você pode ver eventos dos serviços confiáveis. Você pode ver eventos diferentes para quando o runasync de serviço é iniciado e concluído que normalmente acontece em implantações e atualizações. 

![Reliable Services de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Os eventos de ator confiável podem ser exibidos de forma semelhante. Para configurar os eventos mais detalhados para atores confiáveis, você precisa alterar o `scheduledTransferKeywordFilter` na configuração para a extensão de diagnóstico (mostrada abaixo). Detalhes sobre os valores deles estão em [referência de eventos de atores confiáveis](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A linguagem de consulta Kusto é eficiente. Outra consulta valiosa que você pode executar é descobrir quais nós estão gerando a maioria dos eventos. A consulta na captura de tela abaixo mostra os eventos operacionais do Service Fabric agregados ao serviço e ao nó específico.

![Eventos de Consulta por nó](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Próximas etapas

* Para habilitar o monitoramento de infraestrutura, ou seja, contadores de desempenho, vá para [Adicionando o agente do Log Analytics](service-fabric-diagnostics-oms-agent.md). O agente coleta contadores de desempenho e os adiciona ao workspace existente.
* Para clusters locais, os logs do Azure Monitor oferece um Gateway (HTTP Forward Proxy) que pode ser usado para enviar dados para os logs do Azure Monitor. Leia mais sobre isso na [conectar computadores sem acesso à Internet para os logs do Azure Monitor usando o gateway do Log Analytics](../azure-monitor/platform/gateway.md).
* Configure os [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico.
* Familiarize-se com o [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) recursos oferecidos como parte dos logs do Azure Monitor.
* Obtenha uma visão mais detalhada do logs do Azure Monitor e o que ele oferece, leia [What ' s logs do Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md).
