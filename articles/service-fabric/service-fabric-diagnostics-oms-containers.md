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
ms.openlocfilehash: d5fd55ec93ce07e30e4c6f123f9be8492581053c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972245"
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorar contêineres como Log Analytics
 
Este artigo aborda as etapas necessárias para configurar a solução de monitoramento de contêiner do Azure Log Analytics para exibir eventos de contêiner. Para configurar o cluster para coletar eventos de contêiner, consulte [tutorial passo a passo](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurar a solução de monitoramento de contêineres

> [!NOTE]
> Você precisa ter configurado o Log Analytics para seu cluster, bem como ter o agente do Log Analytics implantado em nós. Se você não fizer isso, siga as etapas em [Configurar o Log Analytics](service-fabric-diagnostics-oms-setup.md) e [Adicione o agente do Log Analytics a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Depois que o cluster é configurado com o Log Analytics e o agente do Log Analytics, implante os contêineres. Aguarde até que os contêineres sejam implantados antes de passar para a próxima etapa.

2. No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e clique no recurso **Solução de monitoramento de contêiner** que aparecerá abaixo da categoria Monitoramento + Gerenciamento.

    ![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução dentro do mesmo workspace que já foi criado para o cluster. Essa alteração automaticamente aciona o agente para iniciar a coleta de dados de docker nos contêineres. Em cerca de 15 minutos, você verá a solução se acender com os registros e estatísticas de entrada, como mostra a imagem abaixo.

    ![Painel de análise de Log básica](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a coleta de vários logs específicos do contêiner que podem ser consultados no Log Analytics ou usados para visualizar indicadores de desempenho. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [solução de contêineres do Log Analytics](../azure-monitor/insights/containers.md).
* Leia mais sobre a orquestração de contêineres no Service Fabric: [Service Fabric e contêineres](service-fabric-containers-overview.md)
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* Configure o Log Analytics para definir regras de [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico