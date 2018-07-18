---
title: Migração do Hub IoT do Azure para as configurações de diagnóstico | Microsoft Docs
description: Como atualizar o Hub IoT do Azure para usar as configurações de diagnóstico do Azure em vez do monitoramento de operações para monitorar o status das operações no seu Hub IoT em tempo real.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 85bdb4b4802283c591e4d7a9e8b14ae74fa44e8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666715"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrar seu Hub IoT do monitoramento de operações para as configurações de diagnóstico

Os clientes que usam o [monitoramento de operações][lnk-opsmon] para acompanhar o status das operações no Hub IoT podem migrar o fluxo de trabalho para [as configurações de diagnóstico do Azure][lnk-diagnostics-settings], um recurso do Azure Monitor. As configurações de diagnóstico fornecem informações de diagnóstico de nível de recurso para muitos serviços do Azure.

A funcionalidade de monitoramento de operações do Hub IoT foi preterida e será removida no futuro. Este artigo fornece etapas para mover suas cargas de trabalho do monitoramento de operações para as configurações de diagnóstico. Para obter mais informações sobre a linha do tempo de substituição, consulte [Monitorar suas soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Atualizar Hub IoT

Para atualizar o Hub IoT no portal do Azure, primeiro, ative as configurações de diagnóstico e desligue o monitoramento de operações.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desligue o monitoramento de operações

Depois de testar as novas configurações de diagnóstico em seu fluxo de trabalho, desligue o recurso de monitoramento das operações. 

1. No menu do Hub IoT, selecione **Monitoramento de operações**.
1. Em cada categoria de monitoramento, selecione **Nenhum**.
1. Salvar alterações no monitoramento de operações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar os aplicativos que usam o monitoramento de operações

Os esquemas para monitoramento de operações e configurações de diagnóstico variam um pouco. É importante que você atualize os aplicativos que usam atualmente o monitoramento de operações para mapear para o esquema usado pelas configurações de diagnóstico. 

Além disso, as configurações de diagnóstico oferecem o controle de cinco novas categorias. Depois de atualizar aplicativos para o esquema existente, adicione as novas categorias também:

- Operações de dispositivo gêmeo para nuvem
- Operações de gêmeos de dispositivo para nuvem
- Consultas de gêmeos
- Operações de trabalhos
- Métodos diretos

Para as estruturas de esquema específicas, consulte [Compreender o esquema para as configurações de diagnóstico][lnk-diagnostics-schema].

## <a name="next-steps"></a>Próximas etapas

- [Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/
[lnk-monitor]: iot-hub-monitor-resource-health.md
