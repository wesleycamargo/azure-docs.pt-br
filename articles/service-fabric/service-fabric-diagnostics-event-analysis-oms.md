---
title: Análise de Eventos do Service Fabric do Azure com o Log Analytics | Microsoft Docs
description: Saiba mais sobre visualização e análise de eventos utilizando o Log Analytics para o monitoramento e diagnóstico de clusters do Azure Service Fabric.
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 332939710517e99aaa77642dc5e67256b476bd66
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634568"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Visualização e análise de eventos com o Log Analytics
 O Log Analytics coleta e analisa a telemetria dos aplicativos e serviços hospedados na nuvem e fornece as ferramentas de análise para ajudar a maximizar sua disponibilidade e desempenho. Este artigo descreve como executar consultas no Log Analytics para obter informações e solucionar problemas que está acontecendo em seu cluster. As seguintes perguntas comuns são abordadas:

* Como solucionar problemas de eventos de integridade?
* Como saber quando um nó fica inoperante?
* Como saber se os serviços do meu aplicativo foram iniciados ou interrompidos?

## <a name="log-analytics-workspace"></a>Workspace do Log Analytics

>[!NOTE] 
>Enquanto o armazenamento de diagnóstico é habilitado por padrão no momento da criação do cluster, você deve ainda configurar o espaço de trabalho do Log Analytics para ler do armazenamento de diagnóstico.

O Log Analytics coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. O Log Analytics dá suporte a eventos, dados de desempenho ou outros dados personalizados. Confira [Etapas para configurar a extensão de diagnóstico para eventos agregados](service-fabric-diagnostics-event-aggregation-wad.md) e [Etapas para criar um workspace do Log Analytics para ler os eventos no armazenamento](service-fabric-diagnostics-oms-setup.md) para certificar-se de que os dados estão fluindo para o Log Analytics.

Depois que dados são recebidos pelo Log Analytics, o Azure tem várias *soluções de gerenciamento* que são soluções pré-empacotados ou painéis operacionais para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Este artigo descreve como usar a solução de Análise do Service Fabric, que é criada com o workspace.

## <a name="access-the-service-fabric-analytics-solution"></a>Acesse a solução de Análise do Service Fabric

1. No Portal do Azure, vá para o grupo de recursos em que você criou a solução Análise do Service Fabric.

2. Selecione o recurso **ServiceFabric\<nomeDoEspaçoDeTrabalhoOMS\>**.

2. No `Summary`, você verá o bloco na forma de um gráfico para cada uma das soluções habilitadas, incluindo uma para o Service Fabric. Clique no grafo do **Service Fabric** (primeira imagem abaixo) para prosseguir para a solução Análise do Service Fabric (segunda imagem abaixo).

    ![Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

A imagem acima é a home page da solução de Análise do Service Fabric. Este é um instantâneo do que está acontecendo em seu cluster. Se você habilitou o diagnóstico após a criação do cluster, você pode ver eventos para 

* [Eventos de cluster do Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além dos eventos do Service Fabric prontos para uso, é possível coletar eventos do sistema mais detalhados ao [atualizar a configuração da sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Exibir eventos do Service Fabric, incluindo ações em nós

1. Na página Análise do Service Fabric, clique no grafo para **Eventos do Service Fabric**.

    ![Canal Operacional de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Clique em **Lista** para exibir os eventos em uma lista. Uma vez aqui, você verá todos os eventos do sistema que foram coletados. Para referência, esses são os de WADServiceFabricSystemEventsTable na conta de Armazenamento do Azure, e igualmente os eventos de atores e serviços confiáveis que você vê ao lado são dessas respectivas tabelas.
    
    ![Canal Operacional de Consulta](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Como alternativa, você pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para localizar o que você está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As IDs de evento usadas abaixo são encontradas em [referência de eventos do canal operacional](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Você pode consultar em vários outros campos como em nós específicos (computador), o serviço do sistema (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Exibir eventos dos Atores e Serviços Confiáveis do Service Fabric

1. Na página Análise do Service Fabric, clique no grafo para o **Reliable Services**.

    ![Reliable Services de Solução do Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Clique em **Lista** para exibir os eventos em uma lista. Aqui você pode ver eventos dos serviços confiáveis. Você pode ver eventos diferentes para quando o runasync de serviço é iniciado e concluído que normalmente acontece em implantações e atualizações. 

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
* Para clusters locais, o Log Analytics oferece um Gateway (Proxy de Encaminhamento HTTP) que pode ser usado para enviar dados ao Log Analytics. Leia mais sobre isso em [Conectar computadores sem acesso à Internet ao Log Analytics usando o gateway do Log Analytics](../azure-monitor/platform/gateway.md).
* Configure os [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico.
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics.
* Obtenha uma visão mais detalhada do Log Analytics e o que ele oferece, leia [O que é o Log Analytics?](../operations-management-suite/operations-management-suite-overview.md).
