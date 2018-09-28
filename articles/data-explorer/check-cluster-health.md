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
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988420"
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

1. Adicione outras métricas como CPU e cache de memória para medir o uso de recursos pelo cluster.

1. Se precisar de ajuda para diagnosticar problemas com a integridade de um cluster, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com).