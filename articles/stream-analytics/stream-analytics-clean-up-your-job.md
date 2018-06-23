---
title: Limpar o trabalho do Azure Stream Analytics
description: Este artigo é um guia de como excluir trabalhos do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659025"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Limpar o trabalho do Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser facilmente excluídos por meio do portal do Azure, do Azure PowerShell da SDK do Azure para .Net ou pela API REST.

>[!NOTE] 
>Quando o trabalho do Azure Stream Analytics é interrompido, os dados persistem somente no armazenamento de entrada e saída, como no Hubs de Eventos ou no Banco de Dados SQL do Azure. Se for necessário para remover dados do Azure, lembre-se de seguir o processo de remoção para os recursos de entrada e saída do seu trabalho do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar um trabalho no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. Localize o trabalho em execução do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Parar** para interrompê-lo. 

   ![Parar o trabalho](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Excluir um trabalho no portal do Azure

1. Entre no Portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Excluir** para excluí-lo. 

   ![Excluir o trabalho](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou excluir um trabalho usando o PowerShell

Para interromper um trabalho usando o PowerShell, use o cmdlet [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0). Para excluir um trabalho usando o PowerShell, use o cmdlet [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0).

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou excluir um trabalho usando o SDK do Azure para .NET

Para interromper um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Para excluir um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando a API REST

Para interromper um trabalho usando a API REST, confira o método [Parar](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop). Para excluir um trabalho usando a API REST, confira o método [Excluir](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete).