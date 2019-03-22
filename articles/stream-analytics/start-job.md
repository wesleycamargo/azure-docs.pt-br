---
title: Como iniciar um trabalho do Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho do Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886840"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho do Azure Stream Analytics

Você pode iniciar o trabalho do Azure Stream Analytics usando o portal do Azure, o Visual Studio e o PowerShell. Quando você inicia um trabalho, você pode selecionar um intervalo para o trabalho Iniciar a criação de saída. Portal do Azure, Visual Studio e PowerShell têm diferentes métodos para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="azure-portal"></a>Portal do Azure

Navegue até o trabalho no portal do Azure e selecione **iniciar** na página de visão geral. Selecione uma **hora de início da saída do trabalho** e, em seguida, selecione **iniciar**.

Há três opções para **hora de início da saída do trabalho**: *Agora*, *personalizado*, e *na última interrupção*. Selecionando *agora* inicia o trabalho no momento atual. Selecionando *personalizado* permite que você defina um tempo personalizado no passado ou futuro para o trabalho comece. Para retomar um trabalho interrompido sem perda de dados, escolha *na última interrupção*.

## <a name="visual-studio"></a>Visual Studio

Na exibição de trabalho, selecione o botão de seta verde para iniciar o trabalho. Defina as **modo de início de saída de trabalho** e selecione **iniciar**. O status do trabalho será alterado para **executando**.

Há três opções para **modo de início de saída do trabalho**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão é *JobStartTime*.

*JobStartTime* faz com que o ponto de partida do evento de saída de transmitir o mesmo que quando o trabalho é iniciado.

*CustomTime* inicia a saída em uma hora personalizada que é especificada na *OutputStartTime* parâmetro.

*LastOutputEventTime* torna o ponto de partida do fluxo de eventos de saída o mesmo que o evento de última hora de saída.

## <a name="powershell"></a>PowerShell

Use o seguinte cmdlet para iniciar seu trabalho usando o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Há três opções para **OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão é *JobStartTime*.

*JobStartTime* faz com que o ponto de partida do evento de saída de transmitir o mesmo que quando o trabalho é iniciado.

*CustomTime* inicia a saída em uma hora personalizada que é especificada na *OutputStartTime* parâmetro.

*LastOutputEventTime* torna o ponto de partida do fluxo de eventos de saída o mesmo que o evento de última hora de saída.

Para obter mais informações sobre o `Start-AzStreamAnalyitcsJob` cmdlet, o modo de exibição de [AzStreamAnalyticsJob início referência](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho de Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
