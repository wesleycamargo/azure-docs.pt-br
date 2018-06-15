---
title: Monitorar contêineres em Instâncias de Contêiner do Azure
description: Detalhes sobre como monitorar o consumo de recursos de computação, como CPU e memória, pelos contêineres nas Instâncias de Contêiner do Azure.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: nepeters
ms.openlocfilehash: 814346bd8021b996b64cd7f0311f31b13b32a8c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180395"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorar os recursos de contêiner em Instâncias de Contêiner do Azure

O Azure Monitor fornece insights sobre os recursos de computação usados pelas instâncias de contêineres. Use o Azure Monitor para acompanhar a utilização de CPU e de memória de grupos de contêineres e seus contêineres. Esses dados de uso de recursos ajudam a determinar as melhores configurações de CPU e de memória para os grupos de contêineres.

Este documento fornece detalhes sobre a coleta do uso de CPU e de memória para instâncias de contêiner usando o portal do Azure e a CLI do Azure.

> [!IMPORTANT]
> Neste momento, as métricas de uso de recursos estão disponíveis apenas para contêineres do Linux.
>

## <a name="available-metrics"></a>Métricas disponíveis

O Azure Monitor fornece métricas sobre o uso de **CPU** e **memória** para Instâncias de Contêiner do Azure. Ambas as métricas estão disponíveis para um grupo de contêineres e contêineres individuais.

As métricas de CPU são expressas em **millicores**. Um millicore é 1/1.000º de um núcleo de CPU e, portanto, 500 millicores (ou 500 m) representam 50% de utilização de um núcleo de CPU.

As métricas de memória são expressas em **bytes**.

## <a name="get-metrics---azure-portal"></a>Obter métricas – portal do Azure

Quando um grupo de contêineres é criado, os dados do Azure Monitor ficam disponíveis no portal do Azure. Para ver as métricas de um grupo de contêineres, selecione o grupo de recursos e, em seguida, o grupo de contêineres. Aqui você pode ver gráficos pré-criados para uso de CPU e de memória.

![gráfico duplo][dual-chart]

Se você tiver um grupo de contêineres que contém vários contêineres, use uma [dimensão][monitor-dimension] para apresentar as métricas para cada contêiner individual. Para criar um gráfico com métricas individuais do contêiner, execute as seguintes etapas:

1. Selecione **Monitor** no menu de navegação à esquerda.
2. Selecione um grupo de contêineres e uma métrica (CPU ou Memória).
3. Selecione o botão verde de dimensão e o **Nome do Contêiner**.

![dimensão][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas – CLI do Azure

O uso de CPU e de memória das instâncias de contêiner também pode ser obtido por meio da CLI do Azure. Primeiro, obtenha a ID do grupo de contêineres usando o comando a seguir. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contêineres.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Use o comando a seguir para obter as métricas de uso de **CPU**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

E o comando a seguir para obter as métricas de uso da **memória**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

Para um grupo de vários contêineres, a dimensão `containerName` pode ser adicionada para retornar esses dados por contêiner.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Monitoramento do Azure na [Visão geral do Monitoramento do Azure][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring-and-diagnostics/monitoring-metric-charts.md#what-are-multi-dimensional-metrics
