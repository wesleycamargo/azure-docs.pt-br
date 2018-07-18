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
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: b51f7dc43f390152b2b0be223541e381bbddd3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Visualização e análise de eventos com o Log Analytics

O Log Analytics, também conhecido como OMS (Operations Management Suite), é uma coleção de serviços de gerenciamento que ajudam no monitoramento e diagnóstico para aplicativos e serviços hospedados na nuvem. Este artigo descreve como executar consultas no Log Analytics para obter informações e solucionar problemas que está acontecendo em seu cluster. As seguintes perguntas comuns são abordadas:

* Como solucionar problemas de eventos de integridade?
* Como saber quando um nó fica inoperante?
* Como saber se os serviços do meu aplicativo foram iniciados ou interrompidos?

## <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics

O Log Analytics coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. O Log Analytics dá suporte a eventos, dados de desempenho ou outros dados personalizados. Confira [Etapas para configurar a extensão de diagnóstico para eventos agregados](service-fabric-diagnostics-event-aggregation-wad.md) e [Etapas para criar um espaço de trabalho do Log Analytics para ler os eventos no armazenamento](service-fabric-diagnostics-oms-setup.md) para certificar-se de que os dados estão fluindo para o Log Analytics.

Depois que dados são recebidos pelo Log Analytics, o Azure tem várias *Soluções de Gerenciamento*, que são soluções pré-empacotados para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Este artigo descreve como usar a solução de Análise do Service Fabric, que é criada com o espaço de trabalho.

## <a name="access-the-service-fabric-analytics-solution"></a>Acesse a solução de Análise do Service Fabric

1. Vá para o grupo de recursos em que você criou a solução Análise do Service Fabric. Selecione o recurso **ServiceFabric\<nomeDoEspaçodeTrabalhoOMS\>** e vá para a página de visão geral.

2. Na página de visão geral, clique no link na parte superior para ir para o portal do OMS

    ![Link do Portal do OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-portal-link.png)

3. Você agora está no portal do OMS e pode ver as soluções habilitadas. Clique no gráfico intitulado Service Fabric (primeira imagem abaixo) para ser dirigido para a solução do Service Fabric (segunda imagem abaixo)

    ![Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/oms-workspace-all-solutions.png)

    ![Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new.png)

A imagem acima é a home page da solução de Análise do Service Fabric. Este é um instantâneo do que está acontecendo em seu cluster. Se você habilitou o diagnóstico após a criação do cluster, você pode ver eventos para 

* [Canal operacional](service-fabric-diagnostics-event-generation-operational.md): operações de nível superior executadas pela plataforma do Service Fabric (coleção de serviços do sistema).
* [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Além do canal operacional, eventos de sistema mais detalhados podem ser coletado por [Atualizando a configuração de sua extensão de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

### <a name="view-operational-events-including-actions-on-nodes"></a>Exibir eventos operacionais incluindo ações em nós

1. Na página de Análise do Service Fabric no portal do OMS, clique no gráfico para o Canal Operacional

    ![Canal Operacional da Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Clique na tabela para exibir os eventos em uma lista. Uma vez aqui, você verá todos os eventos do sistema que foram coletados. Para referência, esses são os de WADServiceFabricSystemEventsTable na conta de Armazenamento do Azure, e igualmente os eventos de atores e serviços confiáveis que você vê ao lado são dessas respectivas tabelas.
    
    ![Canal Operacional de Consulta do OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

Como alternativa, você pode clicar na lupa à esquerda e usar a linguagem de consulta Kusto para localizar o que você está procurando. Por exemplo, para localizar todas as ações executadas em nós no cluster, você pode usar a consulta a seguir. As identificações de evento usadas abaixo são encontradas em [referência de eventos do canal operacional](service-fabric-diagnostics-event-generation-operational.md)

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Você pode consultar em vários outros campos como em nós específicos (computador), o serviço do sistema (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Exibir eventos dos Atores e Serviços Confiáveis do Service Fabric

1. Na página de Análise do Service Fabric no portal do OMS, clique no gráfico para o Reliable Services

    ![Reliable Services da Solução OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Clique na tabela para exibir os eventos em uma lista. Aqui você pode ver eventos dos serviços confiáveis. Você pode ver eventos diferentes para quando o runasync de serviço é iniciado e concluído que normalmente acontece em implantações e atualizações. 

    ![Reliable Services de Consulta do OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Os eventos de ator confiável podem ser exibidos de forma semelhante. Para configurar os eventos mais detalhados para atores confiáveis, você precisa alterar o `scheduledTransferKeywordFilter` na configuração para a extensão de diagnóstico (mostrada abaixo). Detalhes sobre os valores para os mesmos estão em [referência de eventos de atores confiáveis](service-fabric-reliable-actors-diagnostics.md#keywords)

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

A linguagem de consulta Kusto é eficiente. Outra consulta valiosa que você pode executar é descobrir quais nós estão gerando a maioria dos eventos. A consulta na captura de tela abaixo mostra os eventos de serviços confiáveis agregado com o nó e serviço específico

![Eventos de Consulta do OMS por nó](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>Próximas etapas

* Para habilitar o monitoramento de infraestrutura, ou seja, contadores de desempenho, vá para [Adicionando o agente do OMS](service-fabric-diagnostics-oms-agent.md). O agente coleta contadores de desempenho e os adiciona ao espaço de trabalho existente.
* Para clusters locais, o OMS oferece um Gateway (Proxy de Encaminhamento HTTP) que pode ser usado para enviar dados ao OMS. Leia mais sobre isso em [Conectar computadores sem acesso à Internet ao OMS usando o Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configure o OMS para definir [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* Obtenha uma visão mais detalhada do Log Analytics e o que ele oferece, leia [O que é o Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
