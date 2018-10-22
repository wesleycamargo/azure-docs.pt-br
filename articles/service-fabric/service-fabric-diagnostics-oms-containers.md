---
title: Monitorar contêineres no Azure Service Fabric com o Log Analytics | Microsoft Docs
description: Use o Log Analytics para monitorar os contêineres em execução em clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 79d30a47b017379107b63b0006a35534f68c43b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210769"
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorar contêineres como Log Analytics
 
Este artigo aborda as etapas necessárias para configurar a solução de monitoramento do contêiner do OMS Log Analytics para exibir eventos de contêiner. Para configurar o cluster para coletar eventos de contêiner, consulte [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa configurar o Log Analytics para o cluster da mesma forma que tem o Agente do OMS implantado nos nós. Se você não fizer isso, siga as etapas em [Set-up Log Analytics](service-fabric-diagnostics-oms-setup.md) (Configurar o Log Analytics do OMS) e [Add the OMS Agent to a cluster](service-fabric-diagnostics-oms-agent.md) (Adicionar o Agente do OMS a um cluster) primeiro.

1. Depois que o cluster for configurado com o Log Analytics e o Agente do OMS, implante os contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo workspace que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos aproximadamente, você deve ver a solução se ilumina com estatísticas e logs de entrada, conforme mostrado na imagem abaixo.

    ![Painel do OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente habilita a coleta de vários logs de contêineres específicos que podem ser consultados no OMS ou usado para indicadores de desempenho visualizados. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [solução de contêineres do OMS](../log-analytics/log-analytics-containers.md).
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* Configure o Log Analytics para definir regras de [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico