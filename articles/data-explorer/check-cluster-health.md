---
title: Verificar a integridade de um cluster do Azure Data Explorer
description: Este artigo descreve as etapas para determinar se o cluster do Azure Data Explorer está íntegro.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d07873b34a41ff20b5007a88743f6b150d4d8a3d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212810"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Verificar a integridade de um cluster do Azure Data Explorer

Há vários fatores que afetam a integridade de um cluster do Azure Data Explorer, incluindo CPU, memória e o subsistema do disco. Este artigo mostra algumas etapas básicas que você pode tomar para medir a integridade de um cluster.

1. Entre em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. No painel esquerdo, selecione o cluster e execute o comando a seguir.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Uma saída de 1 é íntegra; uma saída de 0 é não íntegra.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o cluster.

1. Em **Monitoramento**, selecione **Métricas** e, em seguida, selecione **Keep Alive**, conforme mostrado na imagem a seguir. Uma saída próxima de 1 significa que um cluster íntegro.

    ![Métrica Keep Alive do cluster](media/check-cluster-health/portal-metrics.png)

1. É possível adicionar outras métricas no gráfico. Selecione o gráfico, em seguida, **adicionar métrica**. Selecione outra métrica - Este exemplo mostra **CPU**.

    ![Adicionar métrica](media/check-cluster-health/add-metric.png)

1. Se precisar de ajuda para diagnosticar problemas com a integridade de um cluster, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).