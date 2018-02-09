---
title: "Visão geral de diagnóstico e monitoramento do Azure Service Fabric| Microsoft Docs"
description: "Saiba mais sobre monitoramento e diagnóstico para clusters, aplicativos e serviços do Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 1da2fab92c6fd51c5fc18589da5f8bbec2929503
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico no Azure Service Fabric

Este artigo fornece uma visão geral de configuração de monitoramento e diagnóstico para seus aplicativos executados em clusters do Service Fabric. O monitoramento e o diagnóstico são essenciais para o desenvolvimento, os testes e a implantação de cargas de trabalho em qualquer ambiente. O monitoramento permite controlar como os aplicativos são usados, a utilização de recursos e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar e corrigir quaisquer problemas do cluster e para ajudar a impedir problemas no futuro. 

As principais metas do monitoramento e diagnóstico são:
* Detectar e diagnosticar problemas de infraestrutura
* Detectar problemas com seu aplicativo
* Compreender o consumo de recursos
* Monitorar o desempenho de aplicativos, serviços e infraestrutura

Em um cluster do Service Fabric, os dados de monitoramento e diagnóstico vêm em três níveis: aplicativo, a plataforma (cluster) e a infraestrutura. 
* O [nível de aplicativo](service-fabric-diagnostics-event-generation-app.md) inclui dados sobre o desempenho de seus aplicativos e qualquer log personalizado adicional que você adicionou. Dados de monitoramento do aplicativo devem terminar em Application Insights (AI) do próprio aplicativo. Ele pode vir por meio do SDK do AI, EventFlow ou outro pipeline de sua escolha.
* O [nível plataforma](service-fabric-diagnostics-event-generation-infra.md) inclui eventos de ações que estão sendo executadas no cluster, relacionada ao gerenciamento de cluster e os aplicativos em execução. Os dados de monitoramento de plataforma devem ser enviados para o Log Analytics do OMS, com a solução Service Fabric Analytics para ajudar a visualizar os eventos de entrada. Esses dados são normalmente enviados para uma conta de armazenamento através da extensão Diagnóstico do Azure para Windows ou Linux, de onde ela é acessada pelo Log Analytics do OMS. 
* O nível de infraestrutura enfoca o [monitoramento de desempenho](service-fabric-diagnostics-event-generation-perf.md), visualizando as principais métricas e contadores de desempenho para determinar a utilização de recursos e de carga. O monitoramento de desempenho pode ser obtido usando um agente - é recomendável usar o agente do OMS (Microsoft Monitoring), para que os dados de desempenho acabem no mesmo local que os eventos de plataforma, que permitem uma melhor experiência de diagnóstico como correlacionar alterações em um cluster. 

![Gráfico de visão geral do diagnóstico](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Cenários de monitoramento

Esta seção aborda os principais cenários de monitoramento de um cluster do Service Fabric - aplicativo, cluster, desempenho e monitoramento de integridade. Para cada cenário, a abordagem de tentativa e geral para o monitoramento é abordada. Mais detalhes sobre essas e outras recomendações gerais de monitoramentos para recursos do Azure podem ser encontrados em [Práticas Recomendadas - Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Esses cenários são também livremente mapeados para os três níveis de dados de monitoramento e diagnóstico como discutido acima, ou seja, para cada cenário a ser tratado apropriadamente no cluster, você deve ter dados de monitoramento e diagnóstico recebidos no nível correspondente. O cenário de monitoramento de integridade é uma exceção, pois ele abrange o cluster e tudo que estiver em execução.

## <a name="application-monitoring"></a>Monitoramento de aplicativo
O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo que você criou estão sendo usados. Você deseja monitorar seus aplicativos para garantir que os problemas que impactam os usuários são capturados. Monitorar os seus aplicativos pode ser útil em:
* determinar a carga do aplicativo e usuário de tráfego - você precisa dimensionar seus serviços para atender às demandas de usuário ou solucionar um gargalo potencial em seu aplicativo?
* identificar problemas com a comunicação remota do serviço em seu cluster
* descobrir o que seus usuários estão fazendo com que seu aplicativo - instrumentar seus aplicativos pode ajudar a orientar o desenvolvimento futuro de recursos e melhorar diagnósticos para erros de aplicativo

Para o monitoramento no nível do aplicativo no Service Fabric, recomendamos que você use o Application Insights (AI). A integração do AI com o Service Fabric inclui experiências de ferramentas para Visual Studio e o portal do Azure e uma compreensão do contexto do serviço Service Fabric e da comunicação remota no painel do AI e mapa do aplicativo, levando a uma experiência abrangente de registro pronto para usar. Embora muitos logs são automaticamente criados e coletados para você ao usar o AI, é recomendável que você adicione também o log personalizado para seus aplicativos e faça com que eles apareçam no AI junto com o que é fornecido para criar uma experiência mais rica de diagnóstico para lidar com problemas no futuro. Saiba mais sobre como usar o AI com o Service Fabric na [Análise de Eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Para começar a instrumentar seu código para monitorar seus aplicativos, vá para [Geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Monitorando a disponibilidade de aplicativo
É possível que tudo no seu cluster pareça estar funcionando conforme o esperado e esteja relatando como íntegro, mas seus aplicativos não estão acessíveis ou estão inacessíveis. Embora não sejam muitos os casos em que isso acontece, é importante saber quando isso acontece para ser capaz de resolvê-lo o mais breve possível. O monitoramento de disponibilidade em larga escala está enfocado em monitorar a disponibilidade dos componentes do sistema para entender a "tempo de atividade" geral do sistema. Em um cluster, vamos nos concentrar na disponibilidade na perspectiva do seu aplicativo - a plataforma funciona para garantir que os cenários de gerenciamento do ciclo de vida do aplicativo não causem inatividade. No entanto, diversos problemas no cluster podem afetar o tempo de atividade do aplicativo e nesses casos, como um proprietário de aplicativo, você geralmente deseja saber imediatamente. É recomendável que junto com todos os outros aplicativos, você implante um watchdog no seu cluster. A finalidade desse watchdog seria verificar os endpoints apropriados para seu aplicativo em um intervalo de tempo definido e relatar que estão acessíveis. Você também pode fazer isso usando um serviço externo que executa ping no endpoint e retorna um relatório no intervalo de tempo determinado.

## <a name="cluster-monitoring"></a>Monitoramento do cluster
Monitorar o cluster do Service Fabric é essencial para garantir que a plataforma e todas as cargas de trabalho em execução estejam em execução conforme esperado. Uma das metas do Service Fabric é manter os aplicativos em execução por meio de falhas de hardware. Isso é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas, neste caso específico, e se os próprios serviços do sistema tiverem problemas? Ou, se na tentativa de mover uma carga de trabalho, as regras para o posicionamento dos serviços forem violadas? O monitoramento do cluster permite que você se mantenha informado sobre a atividade ocorrendo em seu cluster, o que ajuda a diagnosticar problemas e corrigi-los com eficiência. Alguns conceitos importantes que você deseja procurar são:
* O Service Fabric está se comportando da maneira esperada, em termos de colocação de seus aplicativos e solução de balanceamento de cluster? 
* As ações que estão sendo feitas para modificar a configuração do cluster estão sendo confirmadas e operadas conforme o esperado? Isso é especialmente relevante ao dimensionar um cluster.
* O Service Fabric está tratando os dados e a comunicação de serviço dentro do cluster corretamente?

O Service Fabric fornece um conjunto abrangente de eventos pronto para usar, por meio dos canais operacional e dados e mensagens. No Windows, eles estão na forma de um único provedor ETW com um conjunto de `logLevelKeywordFilters` relevantes usado para escolher entre os canais diferentes. No Linux, todos os eventos de plataforma são fornecidos por meio de LTTng e são colocados em uma tabela, de onde eles podem ser filtrados conforme necessário. 

Esses canais contém eventos curados e estruturados que podem ser usados para entender melhor o estado do cluster. O "Diagnóstico" é habilitado por padrão no momento da criação do cluster, que pode ser configurado com uma tabela de Armazenamento do Azure onde os eventos desses canais são enviados para a consulta no futuro. Você pode saber mais sobre como monitorar o seu cluster em [Geração de log e eventos de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md). É recomendável que você use o Log Analytics do OMS para monitorar seu cluster. O Log Analytics do OMS oferece uma solução específica do Service Fabric, o Service Fabric Analytics, que fornece um painel personalizado para monitorar clusters do Service Fabric e permite que você consulte eventos do cluster e configure alertas. Leia mais sobre isso em [Análise de eventos com o OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdogs
Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços, executar ping de endpoints e informar a integridade de qualquer coisa no cluster. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de watchdog de exemplo [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Monitoramento de desempenho
Monitoramento de sua infraestrutura subjacente é uma parte importante para compreender o estado do seu cluster e a utilização de recursos. Medir o desempenho do sistema depende de vários fatores, normalmente, cada um deles é medido por meio de um indicador chave de desempenho (KPIs). KPIs relevantes do Service Fabric podem ser mapeados para as métricas que podem ser coletadas de nós no cluster, como contadores de desempenho.
Esses KPIs podem ajudar com:
* compreender a utilização de recursos e carga - com a finalidade de dimensionar o cluster ou otimizar os processos de serviço
* prever problemas de infraestrutura - muitos problemas são precedidos por alterações repentinas (quedas) no desempenho, então você pode usar KPIs, como utilização de CPU e I/O de rede para prever e diagnosticar problemas de infraestrutura

Uma lista de contadores de desempenho que devem ser coletados no nível de infraestrutura pode ser encontrada em [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md). 

Para o monitoramento de desempenho no nível de aplicativo, o Service Fabric fornece um conjunto de contadores de desempenho para os modelos de programação de Atores e Reliable Services. Se você estiver usando qualquer um desses modelos, esses contadores de desempenho podem fornecer KPIs que ajudam a garantir que seus atores estão subindo e descendo corretamente ou se suas solicitações de Reliable Services estão sendo tratadas rápido o suficiente. Consulte [Monitoramento para comunicação remota do Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoramento de desempenho para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters) para obter mais informações sobre estas configurações. Além disso, o Application Insights também tem um conjunto de métricas de desempenho que irá coletar, se configurado com seu aplicativo.

Use o agente do OMS para coletar os contadores de desempenho apropriados e exibir esses KPIs no Log Analytics do OMS. Você também pode usar a extensão do agente de Diagnóstico do Azure (ou qualquer outro agente semelhante) para coletar e armazenar as métricas para análise. 

## <a name="health-monitoring"></a>Monitoramento da integridade
A plataforma do Service Fabric inclui um modelo de integridade, que fornece o relatório de integridade extensível para o status de entidades em um cluster. Cada nó, aplicativo, serviço, partição, réplicas ou instância, tem um status de integridade continuamente atualizável. O status de integridade pode ser "OK", "Aviso" ou "Erro". O status de integridade é alterado por meio de relatórios de integridade que são emitidos para cada entidade, com base em problemas no cluster. O status de integridade de suas entidades pode ser verificado a qualquer momento no Service Fabric Explorer (SFX), conforme mostrado abaixo, ou pode ser consultado por meio da API de integridade das plataformas. Você também pode personalizar relatórios de integridade e modificar o status de integridade de uma entidade adicionando seus próprios relatórios de integridade ou usando a API de integridade. Mais detalhes sobre o modelo de integridade podem ser encontrados no [Introdução ao monitoramento de integridade do Service Fabric](service-fabric-health-introduction.md).

![Painel de integridade SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Além de ver os relatórios de integridade mais recentes no SFX, cada relatório também está disponível como um evento. Eventos de integridade podem ser coletados por meio do canal operacional (consulte [Agregação de eventos com o Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)) e armazenados no Log Analytics do OMS para alertas e consultas no futuro. Isso ajuda a detectar problemas que podem afetar a disponibilidade do seu aplicativo, portanto, é recomendável que você configure alertas para cenários de falha apropriados (alertas personalizados por meio de OMS).

## <a name="monitoring-workflow"></a>Monitoramento de fluxo de trabalho 

O fluxo de trabalho geral do monitoramento e diagnóstico consiste em três etapas:

1. **Geração de eventos**: isso inclui eventos (logs, rastreamentos, eventos personalizados) nos níveis de infraestrutura, plataforma (cluster) e aplicativo
2. **Agregação de eventos**: esta etapa é efetivamente o pipeline para seus eventos terminarem em uma ferramenta que você possa analisá-los, que inclui a extensão de Diagnóstico do Azure ou EventFlow
3. **Análise**: eventos precisam estar acessíveis em uma ferramenta, para permitir análise - visualização, consultas, alertas, etc.

Estão disponíveis vários produtos que abrangem essas três áreas, e você é livre para escolher tecnologias diferentes para cada um. É importante garantir que as várias partes trabalhem juntas para fornecer uma solução de monitoramento de ponta a ponta para o cluster.

## <a name="event-generation"></a>Geração de evento

A primeira etapa no fluxo de trabalho de monitoramento e diagnóstico é a criação e a geração de dados de eventos e logs. Esses eventos, logs e contadores de desempenho são emitidos em todos os três níveis: o nível de aplicativo (qualquer instrumentação adicionada aos aplicativos e serviços implantados para o cluster), a plataforma (eventos emitidos do cluster de acordo com a operação do Service Fabric), e o nível de infraestrutura (métricas de desempenho de cada nó). Os dados de diagnóstico que são coletados em cada um desses níveis são personalizáveis, embora Service Fabric habilite alguma instrumentação padrão. 

Leia mais sobre [eventos no nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) e [eventos no nível de aplicativo](service-fabric-diagnostics-event-generation-app.md) para entender o que é fornecido e como adicionar mais instrumentação. A decisão principal que é necessário fazer aqui é como você deseja instrumentar seu aplicativo. Para aplicativos .NET, é recomendável usar ILogger, mas você também pode explorar EventSource, Serilog e outras bibliotecas semelhantes. Para Java, é recomendável usar Log4j. Além dessas, existem várias outras opções disponíveis que podem ser usadas com base na natureza do aplicativo. Fique à vontade para explorar o que seria melhor para o seu caso de uso específico, ou escolha uma com a qual você está mais familiarizado. 

Depois de tomar uma decisão sobre o provedor de log que deseja usar, você precisa garantir que os logs estejam sendo agregados e armazenados corretamente.

## <a name="event-aggregation"></a>Agregação de eventos

Para coletar os logs e eventos que estão sendo gerados pelos aplicativos e o cluster, normalmente, é recomendável usar a [extensão Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) (mais semelhante à coleta de logs baseada em agente) ou o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (coleta de log no processo). Se você estiver usando o Application Insights e está desenvolvendo em .NET ou Java, é recomendável usar o SDK do Application Insights no lugar de EventFlow.

Embora seja possível obter um trabalho semelhante usando apenas um deles, na maioria das situações, combinar o agente de extensão de Diagnóstico do Azure com um pipeline de coleta no processo (AI SDK ou EventFlow) leva a um fluxo de trabalho de monitoramento mais confiável, abrangente. O agente de extensão do Diagnóstico do Azure pode ser seu caminho para os eventos no nível de plataforma, embora você possa usar o EventFlow (coleta em processo) para os logs no nível de aplicativo. 

Caso você deseje usar apenas um deles, aqui estão alguns pontos-chave para ter em mente.
* Coletar logs de aplicativo usando a extensão do Diagnóstico do Azure será uma boa opção para serviços do Service Fabric se o conjunto de origens e destinos de log não for alterado com frequência e se houver um mapeamento direto entre as origens e seus destinos. O motivo para isso é que a configuração de Diagnóstico do Azure ocorre na camada do Resource Manager. Portanto, para fazer alterações significativas na configuração, é necessário atualizar todo o cluster. Isso significa que ele frequentemente acaba sendo menos eficiente do que o AI SDK ou o EventFlow.
* O uso do EventFlow permite que os serviços enviem logs diretamente para uma plataforma de análise e visualização e/ou para o armazenamento. Outras bibliotecas (ILogger, Serilog etc.) que também podem ser usadas para a mesma finalidade, mas EventFlow tem a vantagem de ter foi projetado especificamente para coleta de log em processo e para dar suporte a serviços de Service Fabric. Isso tende a ter várias vantagens em termos de facilidade de implantação e configuração e oferece mais flexibilidade para dar suporte a ferramentas de análise e as bibliotecas de log diferente. 

## <a name="event-analysis"></a>Análise de eventos

Existem várias plataformas excelentes no mercado para análise e visualização de dados de monitoramento e diagnóstico. Os dois que recomendamos são [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) devido a sua integração com o Service Fabric. Você também deve examinar o [Elastic Stack](https://www.elastic.co/products) (especialmente se estiver considerando a execução de um cluster em um ambiente offline), [Splunk](https://www.splunk.com/), ou qualquer outra plataforma de sua preferência. 

Os principais pontos para qualquer plataforma que você escolher devem incluir seu grau de experiência com a interface do usuário e as opções de consulta, a capacidade de visualizar dados e criar painéis facilmente legíveis e as ferramentas adicionais que eles oferecem para aprimorar o monitoramento, como alertas automatizados.

Além da plataforma que você escolhe, ao configurar um cluster do Service Fabric como um recurso do Azure, você também obtém acesso ao monitorando o desempenho do Azure pronto para uso para suas máquinas.

### <a name="azure-monitor"></a>Azure Monitor

Você pode usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para monitorar muitos dos recursos do Azure em que um cluster de Service Fabric é criado. Um conjunto de métricas para o [conjunto de dimensionamento de máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e para [máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuais é automaticamente coletado e exibido no portal do Azure. Para exibir as informações coletadas, no portal do Azure, selecione o grupo de recursos que contém o cluster de Service Fabric. Em seguida, selecione o conjunto de dimensionamento de máquinas virtuais que você deseja exibir. Na seção **Monitoramento** (no painel de navegação esquerdo), selecione **Métricas** para exibir um gráfico dos valores.

![Exibição de informações de métrica coletadas no portal do Azure](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar os gráficos, siga as instruções em [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Você também pode criar alertas com base nessas métricas, conforme descrito em [criar alertas no Azure Monitor para serviços do Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como monitorar a plataforma e os eventos que o Service Fabric fornece para você em [Geração de log e evento nível de plataforma](service-fabric-diagnostics-event-generation-infra.md)
* Para começar a instrumentar seus aplicativos, consulte [Geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md)
* Siga as etapas para configurar o AI para seu aplicativo com [Monitorar e diagnosticar um aplicativo do ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* Saiba como configurar o Log Analytics do OMS para monitoramento de Contêineres - [Monitoramento e diagnóstico para contêineres do Windows no Service Fabric do Azure](service-fabric-tutorial-monitoring-wincontainers.md)

