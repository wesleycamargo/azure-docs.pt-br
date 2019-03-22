---
title: Monitorar contêineres no Azure Service Fabric com os logs do Azure Monitor | Microsoft Docs
description: Use os logs do Azure Monitor para monitorar os contêineres em execução em clusters do Service Fabric.
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
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d5840db718191c9b67a8b28a2efccd55146ae510
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246926"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorar contêineres com os logs do Azure Monitor
 
Este artigo aborda as etapas necessárias para configurar o solução de monitoramento de contêiner do Azure Monitor logs para exibir eventos de contêiner. Para configurar o cluster para coletar eventos de contêiner, consulte [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa ter o Azure Monitor logs configurado para seu cluster, bem como tem o agente do Log Analytics implantado em nós. Se você não fizer isso, siga as etapas em [configurar logs do Azure Monitor](service-fabric-diagnostics-oms-setup.md) e [adicione o agente do Log Analytics para um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Depois que o cluster é configurado com os logs do Azure Monitor e o agente do Log Analytics, implante os contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo workspace que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos, você verá a solução se acender com os registros e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel de análise de Log básica](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente habilita a coleta de vários logs de contêineres específicos que podem ser consultadas nos logs do Azure Monitor ou usados para visualizar os indicadores de desempenho. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [solução de contêineres de logs do Azure Monitor](../azure-monitor/insights/containers.md).
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com o [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) recursos oferecidos como parte dos logs do Azure Monitor
* Configurar logs do Azure Monitor para configurar [alertas automatizados](../log-analytics/log-analytics-alerts.md) regras para auxiliar na detecção e diagnóstico