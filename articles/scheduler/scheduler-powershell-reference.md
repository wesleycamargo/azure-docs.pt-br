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
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991089"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referência de cmdlets do PowerShell para o Agendador do Azure

> [!IMPORTANT]
> [O Azure Logic Apps](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está sendo retirado. Para agendar trabalhos, [experimente o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Para criar scripts para criar e gerenciar trabalhos do Agendador e conjuntos de trabalhos, você pode usar os cmdlets do PowerShell. Este artigo lista os principais [cmdlets do PowerShell para o Agendador do Azure](/powershell/module/azurerm.scheduler) com links para seus artigos de referência. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para obter mais informações sobre os [Cmdlets do Azure Resource Manager](/powershell/azure/overview), consulte [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md) (Usando o Azure PowerShell com o Azure Resource Manager).

| Cmdlet | DESCRIÇÃO |
|--------|-------------|
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Desabilita uma coleção de trabalhos. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Habilita uma coleção de trabalhos. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Obtém os trabalhos do Agendador. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Obtém coleções de trabalhos. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Obtém o histórico de trabalhos. |
| [New-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Cria um trabalho HTTP. |
| [New-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Cria uma coleção de trabalhos. |
| [New-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Cria um trabalho de fila do barramento de serviço. |
| [New-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Cria um trabalho de tópico do barramento de serviço. |
| [New-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Cria um trabalho de fila de armazenamento. |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Remove um trabalho do Agendador. |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Remove uma coleção de trabalhos. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica um trabalho HTTP do Agendador. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica uma coleção de trabalhos. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica um trabalho de fila do barramento de serviço. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica um trabalho de tópico do barramento de serviço. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica um trabalho de fila de armazenamento. |
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
* [Crie e agende seu primeiro trabalho - Portal do Azure](scheduler-get-started-portal.md)
* [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)
