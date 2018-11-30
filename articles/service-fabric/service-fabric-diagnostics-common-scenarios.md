---
title: Cenários comuns de diagnóstico do Azure Service Fabric | Microsoft Docs
description: Saiba como solucionar problemas de cenários comuns com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: 146ea6431516f7b78d23cd4b36aff43110639e83
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633088"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosticar cenários comuns com o Service Fabric

Este artigo ilustra os cenários comuns que os usuários encontraram na área de monitoramento e diagnóstico com o Service Fabric. Os cenários apresentados abrangem todas as 3 camadas da malha do serviço: aplicativo, cluster e infraestrutura. Cada solução usa o Application Insights, o Log Analytics e as ferramentas de monitoramento do Azure para concluir cada cenário. As etapas em cada solução dão aos usuários uma introdução sobre como usar o Application Insights e o Log Analytics no contexto do Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Pré-requisitos e recomendações

As soluções neste artigo usará as ferramentas a seguir. Recomendamos que você tenha os itens a seguir instalados e configurados:

* [Application Insights com Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Habilite o Diagnóstico do Azure no seu cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurar um espaço de trabalho do Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agente do Log Analytics para rastrear Contadores de Desempenho](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Como posso ver exceções sem tratamento no meu aplicativo?

1. Navegue até o recurso do Application Insights com o qual seu aplicativo está configurado.
2. Clique em *Pesquisa* no canto superior esquerdo. Em seguida,clique no filtro no painel seguinte.

    ![Visão geral do AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Você verá muitos tipos de eventos (rastreamentos, solicitações, eventos personalizados). Escolha "Exceção" como filtro.

    ![Lista de filtros de AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Clicando em uma exceção na lista, você pode ver mais detalhes, incluindo o contexto do serviço se você estiver usando o SDK do Application Insights do Service Fabric.

    ![Exceção de AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Como visualizo quais chamadas HTTP são usadas em meus serviços?

1. No mesmo recurso do Application Insights, você pode filtrar "solicitações" em vez de exceções e exibir todas as solicitações feitas
2. Se estiver usando o SDK do Application Insights do Service Fabric, você poderá ver uma representação visual dos seus serviços conectados um ao outro e o número de solicitações com êxito e falha. À esquerda, clique em "Mapa do aplicativo"

    ![Folha de mapa do aplicativo AI](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Mapa de aplicativo do AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Para obter mais informações sobre o mapa de aplicativo, visite a [documentação do Mapa do aplicativo](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Como crio um alerta quando um nó falhar

1. Eventos de nó são controlados pelo cluster do Service Fabric. Navegue para o recurso da solução de Análise do Service Fabric chamado **ServiceFabric(NameofResourceGroup)**
2. Clique no gráfico na parte inferior da folha intitulado "Resumo"

    ![Solução do Log Analytics](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Aqui, você tem muitos gráficos e blocos exibindo várias métricas. Clique em um dos gráficos, e ele o levará para a Pesquisa de Logs. Aqui, você pode consultar quaisquer eventos de cluster ou os contadores de desempenho.
4. Insira a consulta a seguir. Essas IDs de evento são encontradas na [Referência de eventos de nó](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Clique em "Nova Regra de Alerta" na parte superior, e agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta no seu método de comunicação escolhido.

    ![Novo alerta do Log Analytics](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Como posso ser avisado de reversões de atualização de aplicativo?

1. Na mesma janela de Pesquisa de Logs, como antes, digite a seguinte consulta para reversões de atualização. Essas IDs de evento são encontradas na [Referência de eventos de aplicativo](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Clique em "Nova Regra de Alerta" na parte superior, e agora, sempre que um evento chegar com base nessa consulta, você receberá um alerta.

## <a name="how-do-i-see-container-metrics"></a>Como posso ver as métricas de contêiner?

A mesma exibição com todos os gráficos, você verá alguns blocos para o desempenho de seus contêineres. Você precisa que o agente do Log Analytics e [solução de Monitoramento de Contêiner](service-fabric-diagnostics-oms-containers.md) para essas peças preencherem.

![Métricas de contêiner do Log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Para instrumentar a telemetria de **dentro** de seu contêiner, você precisará adicionar o [pacote do nuget do Application Insights para contêineres](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Como posso monitorar contadores de desempenho?

1. Depois que você tiver adicionado o Agente do Log Analytics ao cluster de que você precisa para adicionar contadores de desempenho específicos que você deseja controlar. Navegue até a página do Workspace do Log Analytics no portal – na página da solução, a guia de workspace está no menu à esquerda.

    ![Guia de espaço de trabalho do Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Uma vez na página do workspace, clique em "Configurações avançadas" no mesmo menu à esquerda.

    ![Configurações avançadas do Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Clique em Dados > Contadores de Desempenho do Windows (Dados > Contadores de Desempenho do Linux para Máquinas Linux) para começar a coletar contadores específicos de seus nós através do Agente do Log Analytics. Aqui estão exemplos de formato para os contadores a serem adicionados

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    No início rápido, VotingData e VotingWeb são os nomes de processo usados, portanto, como seria o acompanhamento desses contadores

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Contadores de desempenho do Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Isso permitirá que você veja como sua infraestrutura está manipulando as cargas de trabalho e definirá alertas relevantes com base na utilização de recursos. Por exemplo, talvez você queira definir um alerta se a utilização total do processador ficar acima de 90% ou abaixo de 5%. O nome do contador que você usaria para isso é "% do Tempo do Processador". Você poderá fazer isso criando uma regra de alerta para a consulta a seguir:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Como acompanhar o desempenho de meu Reliable Services e Atores?

Para rastrear o desempenho de Reliable Services e Atores em seus aplicativos, você deverá adicionar o Ator do Service Fabric, o Método de Autor, Serviço e os contadores de Método de Serviço também. Você pode adicionar esses contadores de maneira semelhante, como no cenário acima, aqui estão exemplos de contadores de desempenho de serviço e ator confiáveis para adicionar no Log Analytics:

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Verifique esses links para a lista completa de contadores de desempenho em Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) e [Atores](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../application-insights/app-insights-alerts.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção Inteligente no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) realiza uma análise pró-ativa da telemetria enviada ao AI para avisá-lo sobre possíveis problemas de desempenho
* Saiba mais sobre os alertas do [Log Analytics](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico.
* Para clusters locais, o Log Analytics oferece um gateway (Proxy de Encaminhamento HTTP) que pode ser usado para enviar dados ao Log Analytics. Leia mais sobre isso em [Conectar computadores sem acesso à Internet ao Log Analytics usando o gateway do Log Analytics](../azure-monitor/platform/gateway.md)
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* Obtenha uma visão mais detalhada do Log Analytics e o que ele oferece, leia [O que é o Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
