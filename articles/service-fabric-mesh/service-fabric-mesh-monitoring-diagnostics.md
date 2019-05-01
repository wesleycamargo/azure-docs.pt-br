---
title: Monitoramento e diagnóstico em aplicativos de Malha do Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre como monitorar e diagnosticar aplicativos na Malha do Service Fabric no Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939815"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico
A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Monitoramento e diagnóstico para a malha de Service Fabric é categorizado em três principais tipos de dados de diagnóstico:

- Logs de aplicativos - estes são definidos como os logs de aplicativos em contêineres, com base em como você tiver instrumentado seu aplicativo (por exemplo, logs de docker)
- Eventos de plataforma – eventos da plataforma Malha relevantes à sua operação de contêiner, incluindo atualmente encerramento, desativação e a ativação de contêiner.
- Métricas de contêiner - as métricas de desempenho e a utilização de recursos para seus contêineres (estatísticas do docker)

Este artigo discute as opções de monitoramento e diagnóstico para a versão de visualização mais recente disponível.

## <a name="application-logs"></a>Logs de aplicativo

Você pode exibir os logs de docker de seus contêineres implantados, em uma base por contêiner. No modelo de aplicativo de Malha do Service Fabric, cada contêiner é um pacote de código em seu aplicativo. Para ver os logs associados com um pacote de códigos, use o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Você pode usar o comando "az mesh service-replica" para obter o nome da réplica. Nomes de réplica são incrementar números inteiros de 0.

Aqui é com o que isso se parece para ver os logs do contêiner do aplicativo de votação VotingWeb.Code:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contêiner 

O ambiente de malha expõe um punhado de métricas que indicam como os seus contêineres são executados. As métricas a seguir estão disponíveis por meio do Azure monitoram o portal e o Azure CLI:

| Métrica | DESCRIÇÃO | Unidades|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu como uma porcentagem | % |
| MemoryUtilization | ActualMem/AllocatedMem como uma porcentagem | % |
| AllocatedCpu | CPU alocado de acordo com o modelo do Resource Manager | Milinúcleos |
| AllocatedMemory | Memória alocada de acordo com o modelo do Resource Manager | MB |
| ActualCpu | Uso da CPU | Milinúcleos |
| ActualMemory | Uso de memória | MB |
| ContainerStatus | 0 - inválido: O status do contêiner é desconhecido <br> 1 - pendente: O contêiner está agendado para iniciar <br> 2 - Iniciando: O contêiner está iniciando <br> 3 - iniciado: O contêiner for iniciado com êxito <br> 4 - parando: O contêiner está sendo interrompido. <br> 5 - interrompido: O contêiner foi parado com êxito | N/D |
| ApplicationStatus | 0 - desconhecida: O status não é recuperável <br> 1 - pronto: O aplicativo é executado com êxito <br> 2 - atualização: Há uma atualização em andamento <br> 3 - Criando: O aplicativo está sendo criado. <br> 4 - excluindo: O aplicativo está sendo excluído. <br> 5 - falha: Falha ao implantar o aplicativo | N/D |
| ServiceStatus | 0 - inválido: O serviço atualmente não tem um status de integridade <br> 1 - Okey: O serviço está íntegro  <br> 2 - Aviso: Pode haver algo errado que exigem a investigação <br> 3 - Erro: Há algo errado de que precisa de investigação <br> 4 – desconhecido: O status não é recuperável | N/D |
| ServiceReplicaStatus | 0 - inválido: A réplica atualmente não tem um status de integridade <br> 1 - Okey: O serviço está íntegro  <br> 2 - Aviso: Pode haver algo errado que exigem a investigação <br> 3 - Erro: Há algo errado de que precisa de investigação <br> 4 – desconhecido: O status não é recuperável | N/D | 
| RestartCount | Número de reinicializações de contêiner | N/D |

> [!NOTE]
> Os valores ServiceStatus e ServiceReplicaStatus são o mesmo que o [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) no Service Fabric. 

Cada métrica está disponível em dimensões diferentes para que você possa ver agregações em níveis diferentes. A lista atual de dimensões são da seguinte maneira:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> A dimensão Code não está disponível para aplicativos do Linux. 

Cada dimensão corresponde a diferentes componentes do [modelo de aplicativo do Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Monitor do Azure CLI

Uma lista completa de comandos está disponível na [docs da CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , mas a incluímos alguns exemplos úteis abaixo 

Em cada exemplo, a ID de recurso segue este padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização da CPU dos contêineres em um aplicativo

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização de memória para cada réplica de serviço
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicializações para cada contêiner em uma janela de 1 hora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média da CPU em todos os serviços denominado "VotingWeb" em uma janela de 1 hora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Gerenciador de métricas

Metrics explorer é uma folha no portal no qual você pode visualizar todas as métricas para seu aplicativo de malha. Esta folha é acessível na página do aplicativo no portal e a folha do Azure monitor, o último dos quais você pode usar para exibir as métricas para todos os recursos do Azure que dão suporte ao Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas do Azure Monitor, confira a [docs da CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
