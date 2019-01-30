---
title: Visualize seus dados do Azure IoT Central em um painel do Power BI | Microsoft Docs
description: Use o modelo de solução do Power Analytics do Azure IoT Central Analytics para visualizar e analisar os dados da IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: a37eb68d135d156c25c58bf91489d5b50ac2c170
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423113"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise seus dados do Azure IoT Central em um painel do Power BI

*Este tópico aplica-se aos administradores.*

![Pipeline de modelo de solução Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Use o modelo de solução do Power Analytics do Azure IoT Central Analytics para criar um poderoso painel do Power BI para monitorar o desempenho de seus dispositivos IoT. No seu painel do Power BI, você pode:
- Acompanhe quantos dados seus dispositivos estão enviando ao longo do tempo
- Comparar o volume de dados entre telemetria, estados e eventos
- Identifique dispositivos que estão relatando muitas medições
- Observe as tendências históricas das medições do dispositivo
- Identificar dispositivos problemáticos que enviam muitos eventos críticos

Este modelo de solução configura o pipeline que coleta os dados na sua conta de armazenamento do Azure Blob de [Exportação de dados contínua](howto-export-data.md). Esses dados fluem por meio do Azure Functions, Azure Data Factory e o banco de dados do Azure SQL para processar e transformar os dados. A saída pode ser visualizada e analisada em um relatório do Power BI que você pode baixar como um arquivo PBIX. Todos esses recursos são criados em sua assinatura do Azure, para que você possa personalizar cada componente para atender às suas necessidades. Esse modelo de solução é totalmente livre, portanto, você pode saber mais sobre a arquitetura e estenda a solução visitando a [repositório GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsbpsolutiontemplatescomnamemicrosoft-iotcontinuousdataexporttemplate-from-microsoft-appsource"></a>Obtenha o [modelo de solução de Análise do Azure IoT Central](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate) no Microsoft AppSource.

## <a name="prerequisites"></a>Pré-requisitos
A configuração do modelo requer o seguinte:
- Acesso a uma assinatura do Azure
- Exportar dados usando [exportação contínua de dados](howto-export-data.md) do seu aplicativo IoT Central. Recomendamos que você ative medições, dispositivos e fluxos de modelos de dispositivos para aproveitar ao máximo o painel do Power BI.
- Power BI Desktop (versão mais recente)
- Power BI Pro (se você quiser compartilhar o painel com os outros)

## <a name="reports"></a>Relatórios

Dois relatórios são gerados automaticamente. 

O primeiro relatório mostra uma visão histórica das medições relatadas pelos dispositivos e divide os diferentes tipos de medições e dispositivos que enviaram o maior número de medições.

![Página de relatório do Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

O segundo relatório aprofunda os eventos e mostra uma visão histórica dos erros e avisos relatados. Ele também mostra quais dispositivos estão relatando o maior número de eventos de tudo para cima, bem como especificamente os eventos de erro e eventos de aviso.

![Página de relatório do Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Recursos

Visite o AppSource para obter o modelo de solução do [Azure IoT Central Analytics](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate).

Visite o [repositório GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) para saber mais sobre a arquitetura e ampliar a solução.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a visualizar seus dados no Power BI, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como gerenciar dispositivos](howto-manage-devices.md)
