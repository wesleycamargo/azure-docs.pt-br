---
title: Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs
description: Saiba mais sobre monitoramento e diagnóstico para clusters, aplicativos e serviços do Service Fabric do Azure.
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
ms.date: 04/03/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: 03fa2862bbce39ac9ee6b7da02bd93b02b05f216
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico no Azure Service Fabric

Este artigo fornece uma visão geral de monitoramento e diagnóstico do Azure Service Fabric. O monitoramento e o diagnóstico são essenciais para o desenvolvimento, os testes e a implantação de cargas de trabalho em qualquer ambiente. O monitoramento permite controlar como os aplicativos são usados, a utilização de recursos e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar, corrigir quaisquer problemas e impedir problemas no futuro. 

## <a name="application-monitoring"></a>Monitoramento de aplicativo
O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo estão sendo usados. Você deseja monitorar seus aplicativos para garantir que os problemas que impactam os usuários são capturados. Monitorar os seus aplicativos pode ser útil nos seguintes cenários:
* Determinar a carga do aplicativo e usuário de tráfego - você precisa dimensionar seus serviços para atender às demandas de usuário ou solucionar um gargalo potencial em seu aplicativo?
* Identificar problemas com a comunicação remota do serviço em seu cluster
* Descobrir o que seus usuários estão fazendo com que seu aplicativo - a coleta de telemetria em seus aplicativos pode ajudar a orientar o desenvolvimento futuro de recursos e melhorar diagnósticos para erros de aplicativo
* Monitorar o que acontece dentro de seus contêineres em execução

O Service Fabric oferece suporte a muitas opções para instrumentar seu código de aplicativo com telemetria e rastreamentos adequados. É recomendável que você use o Application Insights (AI). A integração do AI com o Service Fabric inclui experiências de ferramentas para Visual Studio e Portal do Azure, bem como métricas específicas do Service Fabric, fornecendo uma experiência de logs abrangente e de pronto uso. Embora muitos logs são automaticamente criados e coletados para você com o AI, é recomendável que você adicione também o log personalizado para seus aplicativos para criar uma experiência mais rica de diagnóstico. Saiba mais sobre como começar a usar o Application Insights com o Service Fabric na [Análise de Eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

![Detalhes de rastreamento do AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

## <a name="platform-cluster-monitoring"></a>Monitoramento da plataforma (cluster)
Monitorar o cluster do Service Fabric é essencial para garantir que a plataforma e todas as cargas de trabalho estejam em execução conforme esperado. Uma das metas do Service Fabric é manter os aplicativos resilientes a falhas de hardware. Esse objetivo é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas, neste caso específico, e se os próprios serviços do sistema tiverem problemas? Ou, se na tentativa de mover uma carga de trabalho, as regras para o posicionamento dos serviços forem violadas? O monitoramento do cluster permite que você se mantenha informado sobre a atividade ocorrendo em seu cluster, o que ajuda a diagnosticar problemas e corrigi-los com eficiência. Alguns conceitos importantes que você deseja procurar são:
* O Service Fabric está se comportando da maneira esperada, em termos de colocação de seus aplicativos e solução de balanceamento de cluster? 
* As ações do usuário são executadas em seu cluster reconhecido e conforme o esperado? Isso é especialmente relevante ao dimensionar um cluster.
* O Service Fabric está tratando os dados e a comunicação de serviço dentro do cluster corretamente?

O Service Fabric fornece um conjunto abrangente de eventos pronto para usar, por meio dos canais operacional e dados e mensagens. No Windows, eles estão na forma de um único provedor ETW com um conjunto de `logLevelKeywordFilters` relevantes usado para escolher entre os canais diferentes. No Linux, todos os eventos de plataforma são fornecidos por meio de LTTng e são colocados em uma tabela, de onde eles podem ser filtrados conforme necessário. 

Esses canais contém eventos curados e estruturados que podem ser usados para entender melhor o estado do cluster. O Diagnóstico é habilitado por padrão no momento da criação do cluster, que cria uma tabela de Armazenamento do Azure onde os eventos desses canais são enviados para a consulta no futuro. Você pode saber mais sobre como monitorar o seu cluster em [Geração de log e eventos de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).

Para coletar os logs e eventos que estão sendo gerados pelo seu cluster, geralmente recomendamos o uso da [extensão de Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md). Isso se integra bem com a solução específica do Service Fabric do Log Analytics do OMS, a Análise do Service Fabric, que fornece um painel personalizado para monitorar clusters do Service Fabric e permite que você consulte eventos do cluster e configure alertas. Leia mais sobre isso em [Análise de eventos com o OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 ![Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>Monitoramento de desempenho
Monitoramento de sua infraestrutura subjacente é uma parte importante para compreender o estado do seu cluster e a utilização de recursos. Medir o desempenho do sistema depende de vários fatores, normalmente, cada um deles é medido por meio de um indicador chave de desempenho (KPIs). KPIs relevantes do Service Fabric podem ser mapeados para as métricas que podem ser coletadas de nós no cluster, como contadores de desempenho.
Esses KPIs podem ajudar com:
* Compreender a utilização de recursos e carga - com a finalidade de dimensionar o cluster ou otimizar os processos de serviço.
* Prever problemas de infraestrutura - muitos problemas são precedidos por alterações repentinas (quedas) no desempenho, então você pode usar KPIs, como utilização de CPU e I/O de rede para prever e diagnosticar problemas de infraestrutura.

Uma lista de contadores de desempenho que devem ser coletados no nível de infraestrutura pode ser encontrada em [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md). 

O Service Fabric fornece um conjunto de contadores de desempenho para os modelos de programação de Atores e Reliable Services. Se você estiver usando qualquer um desses modelos, esses contadores de desempenho podem fornecer KPIs que ajudam a garantir que seus atores estão subindo e descendo corretamente ou se suas solicitações de Reliable Services estão sendo tratadas rápido o suficiente. Para obter mais informações, consulte [Monitoramento para comunicação remota do Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoramento de desempenho para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Além disso, o Application Insights também tem um conjunto de métricas de desempenho que irá coletar, se configurado com seu aplicativo.

Use o [agente do OMS](service-fabric-diagnostics-oms-agent.md) para coletar os contadores de desempenho apropriados e exibir esses KPIs no Log Analytics do OMS.

![Gráfico de visão geral do diagnóstico](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitoramento da integridade
A plataforma do Service Fabric inclui um modelo de integridade, que fornece o relatório de integridade extensível para o status de entidades em um cluster. Cada nó, aplicativo, serviço, partição, réplicas ou instância, tem um status de integridade continuamente atualizável. O status de integridade pode ser "OK", "Aviso" ou "Erro". O status de integridade é alterado por meio de relatórios de integridade que são emitidos para cada entidade, com base em problemas no cluster. O status de integridade de suas entidades pode ser verificado a qualquer momento no Service Fabric Explorer (SFX), conforme mostrado abaixo, ou pode ser consultado por meio da API de integridade das plataformas. Você também pode personalizar relatórios de integridade e modificar o status de integridade de uma entidade adicionando seus próprios relatórios de integridade ou usando a API de integridade. Mais detalhes sobre o modelo de integridade podem ser encontrados no [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md).

![Painel de integridade SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Além de ver os relatórios de integridade mais recentes no SFX, cada relatório também está disponível como um evento. Eventos de integridade podem ser coletados por meio do canal operacional (consulte [Agregação de eventos com o Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) e armazenados no Log Analytics para alertas e consultas no futuro. Isso ajuda a detectar problemas que podem afetar a disponibilidade do seu aplicativo, portanto, é recomendável que você configure alertas para cenários de falha apropriados (alertas personalizados por meio do Log Analytics).

## <a name="other-logging-solutions"></a>Outras soluções de registro em log

Embora as duas soluções que recomendamos, [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), tenham integração interna com o Service Fabric, muitos eventos são gravados por meio de provedores de etw e são extensíveis com outras soluções de registro em log. Você também deve examinar o [Elastic Stack](https://www.elastic.co/products) (especialmente se estiver considerando a execução de um cluster em um ambiente offline), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/) ou qualquer outra plataforma de sua preferência. 

Os principais pontos para qualquer plataforma que você escolher devem incluir seu grau de experiência com a interface do usuário e as opções de consulta, a capacidade de visualizar dados e criar painéis facilmente legíveis e as ferramentas adicionais que eles oferecem para aprimorar o monitoramento, como alertas automatizados.

## <a name="next-steps"></a>Próximas etapas

* Para começar a instrumentar seus aplicativos, consulte [Geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Saiba mais sobre como monitorar a plataforma e os eventos que o Service Fabric fornece para você em [Geração de log e evento nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Siga as etapas para configurar o AI para seu aplicativo com [Monitorar e diagnosticar um aplicativo do ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba como configurar o Log Analytics do OMS para monitoramento de Contêineres - [Monitoramento e diagnóstico para contêineres do Windows no Service Fabric do Azure](service-fabric-tutorial-monitoring-wincontainers.md).
* Saiba mais sobre as recomendações gerais de monitoramentos para recursos do Azure - [Práticas Recomendadas - Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
