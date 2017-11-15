---
title: "Análise de Eventos do Service Fabric do Azure com o OMS | Microsoft Docs"
description: "Saiba mais sobre visualização e análise de eventos utilizando o OMS para o monitoramento e diagnóstico de clusters do Azure Service Fabric."
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 09542c0e7f628ca4fea00a6562c0b9525432c213
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Análise de eventos e visualização com OMS

O OMS (Operations Management Suite) é uma coleção de serviços de gerenciamento que ajudam no monitoramento e diagnóstico para aplicativos e serviços hospedados na nuvem. Para obter uma visão mais detalhada do OMS e o que ele oferece, leia [Novidades do OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics e o espaço de trabalho de OMS

O Log Analytics coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. O Log Analytics dá suporte a eventos, dados de desempenho ou outros dados personalizados.

Quando o OMS for configurado, você terá acesso a um *espaço de trabalho de OMS* específico, de onde os dados poderão ser consultados ou visualizados em painéis.

Depois que dados são recebidos pelo Log Analytics, o OMS tem várias *Soluções de Gerenciamento*, que são soluções pré-empacotados para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Também há várias outras que vale a pena explorar, e o OMS também permite a criação de soluções personalizadas, sobre as quais você pode ler mais [aqui](../operations-management-suite/operations-management-suite-solutions.md). Cada solução que você optar por usar para um cluster pode ser configurada no mesmo espaço de trabalho de OMS, juntamente com o Log Analytics. Os espaços de trabalho permitem painéis personalizados e visualização de dados e modificações nos dados que você deseja coletar, processar e analisar.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configuração de um espaço de trabalho de OMS com a Solução de Análise do Service Fabric
É recomendável que você inclua a Solução do Service Fabric no espaço de trabalho do OMS - ela inclui um painel que mostra os vários canais de log de entrada no nível de plataforma e de aplicativo e fornece a capacidade de consultar logs específicos do Service Fabric. Aqui está a aparência de uma solução do Service Fabric relativamente simples, com um único aplicativo implantado no cluster:

![Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Consulte [Configurar o Log Analytics do OMS](service-fabric-diagnostics-oms-setup.md) para começar a usar isso para seu cluster.

## <a name="using-the-oms-agent"></a>Uso do Agente do OMS

É recomendado usar EventFlow e WAD como soluções de agregação, pois permitem uma abordagem mais modular para diagnósticos e monitoramento. Por exemplo, se você quiser alterar as saídas de EventFlow, não será preciso alterar a instrumentação real, apenas fazer uma modificação simples no arquivo de configuração. Se, no entanto, você decidir investir no uso do Log Analytics do OMS, configure o [agente do OMS](../log-analytics/log-analytics-windows-agents.md). Você também deve usar o agente do OMS ao implantar contêineres ao seu cluster, conforme discutido abaixo. 

Siga para [Adicionar o Agente do OMS a um cluster](service-fabric-diagnostics-oms-agent.md) para consultar as etapas para isso.

As vantagens são as seguintes:

* Dados avançados no lado de métricas e contadores de desempenho
* Métricas fáceis de configurar coletadas do cluster e sem a necessidade de atualizar a configuração do cluster. As alterações nas configurações do agente podem ser feitas no portal do OMS e o agente é reiniciado automaticamente para corresponder à configuração necessária. Para configurar o agente do OMS para selecionar contadores de desempenho específicos, acesse o espaço de trabalho **Página Inicial > Configurações > Dados > Contadores de Desempenho do Windows** e selecione os dados que você deseja coletar
* Os dados são mostrados mais rápido do que se tiverem que ser armazenados antes de serem selecionados pelo OMS/Log Analytics
* A monitoração de contêineres é muito mais fácil, pois é possível selecionar logs de docker (stdout, stderr) e estatísticas (métricas de desempenho nos níveis de contêiner e nó)

A principal consideração é que, como ele é um agente que será implantado no cluster juntamente com todos os seus aplicativos, pode haver algum impacto sobre o desempenho dos aplicativos no cluster.

## <a name="monitoring-containers"></a>Monitoramento de contêineres

Ao implantar contêineres em um cluster do Service Fabric, é recomendável que o cluster tenha sido configurado com o agente do OMS e que a solução de contêineres tenha sido adicionada ao espaço de trabalho de OMS para habilitar o monitoramento e o diagnóstico. Aqui está a aparência de solução de contêineres em um espaço de trabalho:

![Painel do OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente habilita a coleta de vários logs de contêineres específicos que podem ser consultados no OMS ou usado para indicadores de desempenho visualizados. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner

[Monitorar contêineres com Log Analytics do OMS](service-fabric-diagnostics-oms-containers.md) abrange as etapas necessárias para configurar o monitoramento de contêiner para seu cluster. Para saber mais sobre a solução de Contêineres do OMS, consulte sua [documentação](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Próximas etapas

Explore as seguintes ferramentas de OMS e opções para personalizar um espaço de trabalho conforme suas necessidades:

* Para clusters locais, o OMS oferece um Gateway (Proxy de Encaminhamento HTTP) que pode ser usado para enviar dados ao OMS. Leia mais sobre isso em [Conectar computadores sem acesso à Internet ao OMS usando o Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configure o OMS para configurar [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e diagnóstico
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics