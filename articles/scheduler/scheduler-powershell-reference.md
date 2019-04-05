---
title: Referência de cmdlets do PowerShell - Agendador do Azure
description: Saiba mais sobre os cmdlets do PowerShell para o Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044990"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referência de cmdlets do PowerShell para o Agendador do Azure

> [!IMPORTANT]
> [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está sendo desativado. Para agendar trabalhos, [experimente os Aplicativos Lógicos do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar scripts para criar e gerenciar trabalhos do Agendador e conjuntos de trabalhos, você pode usar os cmdlets do PowerShell. Este artigo lista os principais [cmdlets do PowerShell para o Agendador do Azure](/powershell/module/azurerm.scheduler) com links para seus artigos de referência. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para obter mais informações sobre os [Cmdlets do Azure Resource Manager](/powershell/azure/overview), consulte [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Usando o Azure PowerShell com o Azure Resource Manager).

| Cmdlet | DESCRIÇÃO |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Desabilita uma coleção de trabalhos. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Habilita uma coleção de trabalhos. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Obtém os trabalhos do Agendador. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Obtém coleções de trabalhos. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Obtém o histórico de trabalhos. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Cria um trabalho HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Cria uma coleção de trabalhos. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Cria um trabalho de fila do barramento de serviço. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Cria um trabalho de tópico do barramento de serviço. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Cria um trabalho de fila de armazenamento. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Remove um trabalho do Agendador. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Remove uma coleção de trabalhos. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Modifica um trabalho HTTP do Agendador. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Modifica uma coleção de trabalhos. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Modifica um trabalho de fila do barramento de serviço. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Modifica um trabalho de tópico do barramento de serviço. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Modifica um trabalho de fila de armazenamento. |
||| 

Para mais detalhes, você pode executar qualquer um desses cmdlets: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Consulte também

* [O que é o Agendador do Azure?](scheduler-intro.md)
* [Hierarquia de entidades, terminologia e conceitos](scheduler-concepts-terms.md)
* [Criar e agendar o primeiro trabalho - portal do Azure](scheduler-get-started-portal.md)
* [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)
