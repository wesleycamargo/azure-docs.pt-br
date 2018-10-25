---
title: Usar o portal do Azure para monitorar um aplicativo gerenciado | Microsoft Docs
description: Mostra como usar o portal do Azure para monitorar disponibilidade e alertas para um aplicativo gerenciado.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805097"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorar uma instância implantada de um aplicativo gerenciado

Depois de implantar um aplicativo gerenciado em sua assinatura do Azure, você pode querer verificar o status do aplicativo. Este artigo mostra as opções no portal do Azure para verificar o status. Você pode monitorar a disponibilidade dos recursos em seu aplicativo gerenciado. Você também pode configurar e exibir alertas.

## <a name="view-resource-health"></a>Exibir a integridade dos recursos

1. Selecione sua instância do aplicativo gerenciado.

   ![Selecionar aplicativo gerenciado](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **Resource Health**.

   ![Selecionar integridade de recursos](./media/monitor-managed-application-portal/select-resource-health.png)

1. Exiba a disponibilidade dos recursos em seu aplicativo gerenciado.

   ![Exibir a integridade dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Exibir alertas

1. Selecione **Alertas**.

   ![Selecionar alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se você tiver regras de alerta configuradas, você verá informações sobre os alertas que foram gerados.

   ![Exibir alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, selecione **+Nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

É possível criar alertas para a instância do aplicativo gerenciado ou para os recursos no aplicativo gerenciado. Confira mais informações sobre como criar alertas em [Visão geral dos alertas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de aplicativo gerenciado, veja [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para implantar um aplicativo gerenciado, confira [Implantar aplicativo do catálogo de serviços por meio do portal do Azure](deploy-service-catalog-quickstart.md).