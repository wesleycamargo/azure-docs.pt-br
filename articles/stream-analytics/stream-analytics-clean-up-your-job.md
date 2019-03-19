---
title: Limpar o trabalho do Azure Stream Analytics
description: Este artigo mostra diferentes métodos para excluir trabalhos do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e43e1034abe4bbe3d31a46ab3b98b0efe612b852
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588713"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Limpar o trabalho do Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser facilmente excluídos por meio do portal do Azure, do Azure PowerShell da SDK do Azure para .Net ou pela API REST. Um trabalho do Stream Analytics não pode ser recuperado depois que ele foi excluído.

>[!NOTE] 
>Quando o trabalho do Azure Stream Analytics é interrompido, os dados persistem somente no armazenamento de entrada e saída, como no Hubs de Eventos ou no Banco de Dados SQL do Azure. Se for necessário para remover dados do Azure, lembre-se de seguir o processo de remoção para os recursos de entrada e saída do seu trabalho do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar um trabalho no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. Localize o trabalho em execução do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Parar** para interrompê-lo. 

   ![Interromper um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Excluir um trabalho no portal do Azure

1. Entre no Portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Excluir** para excluí-lo. 

   ![Excluir um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou excluir um trabalho usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para interromper um trabalho usando o PowerShell, use o [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet. Para excluir um trabalho usando o PowerShell, use o [AzStreamAnalyticsJob remover](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou excluir um trabalho usando o SDK do Azure para .NET

Para interromper um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Para excluir um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando a API REST

Para interromper um trabalho usando a API REST, confira o método [Parar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Para excluir um trabalho usando a API REST, confira o método [Excluir](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
