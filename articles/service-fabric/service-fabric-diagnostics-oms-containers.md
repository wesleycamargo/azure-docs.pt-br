---
title: "Monitorar contêineres no Azure Service Fabric com o Log Analytics do OMS | Microsoft Docs"
description: "Use o OMS Log Analytics para monitorar os contêineres em execução em clusters do Azure Service Fabric."
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
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Monitorar contêineres como Log Analytics do OMS
 
Este artigo aborda as etapas necessárias para configurar o monitoramento de contêiner para seu cluster. Para obter mais informações sobre isso, consulte [Monitorando contêineres no Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Para ver um tutorial passo a passo sobre isso, você também pode seguir [Monitorar os contêineres do Windows no Service Fabric usando o OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa configurar o Log Analytics do OMS para o cluster da mesma forma que tem o Agente do OMS implantado nos nós. Se você não fizer isso, siga as etapas em [Set-up OMS Log Analytics](service-fabric-diagnostics-oms-setup.md) (Configurar o Log Analytics do OMS) e [Add the OMS Agent to a cluster](service-fabric-diagnostics-oms-agent.md) (Adicionar o Agente do OMS a um cluster) primeiro.

1. Depois que o cluster for configurado com o Log Analytics do OMS e o Agente do OMS, implante os contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo espaço de trabalho que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos aproximadamente, você deve ver a solução se ilumina com estatísticas e logs de entrada.

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* Configure o OMS para configurar regras de [alerta automatizado](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e diagnóstico