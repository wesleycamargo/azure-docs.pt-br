---
title: Visualize seus dados do Azure IoT Central em um painel do Power BI | Microsoft Docs
description: Use o modelo de solução do Power Analytics do Azure IoT Central Analytics para visualizar e analisar os dados da IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 5cb55e73b379b909811bde728d2ab39e29635bf5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190692"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise seus dados do Azure IoT Central em um painel do Power BI

![Pipeline de modelo de solução Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Use o modelo de solução do Power Analytics do Azure IoT Central Analytics para criar um poderoso painel do Power BI para monitorar o desempenho de seus dispositivos IoT. No seu painel do Power BI, você pode:
- Acompanhe quantos dados seus dispositivos estão enviando ao longo do tempo
- Comparar o volume de dados entre telemetria, estados e eventos
- Identifique dispositivos que estão relatando muitas medições
- Observe as tendências históricas das medições do dispositivo
- Identificar dispositivos problemáticos que enviam muitos eventos críticos

Este modelo de solução configura o pipeline que coleta os dados em sua conta de armazenamento do Azure Blob da [exportação contínua de dados](howto-export-data.md). Esses dados fluem para o Azure Functions, o Azure Data Factory e o Banco de Dados SQL do Azure, que processam e transformam os dados a serem visualizados e analisados em um relatório do Power BI que você pode baixar como um arquivo PBIX. Todos esses recursos são criados em sua assinatura do Azure, para que você possa personalizar cada componente para atender às suas necessidades. Esse modelo de solução é totalmente de código aberto, para que você possa aprender mais sobre a arquitetura e estender a solução visitando o [repositório do Github](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

**[Obtenha o modelo de solução do Azure IoT Central Analytics do Microsoft AppSource.](https://aka.ms/iotcentralpowerbisolutiontemplate)**

## <a name="prerequisites"></a>Pré-requisitos
A configuração do modelo requer o seguinte:
- Acesso a uma assinatura do Azure
- Dados exportados usando [exportação contínua de dados ](howto-export-data.md)do seu aplicativo IoT Central. Recomendamos que você ative medições, dispositivos e fluxos de modelos de dispositivos para aproveitar ao máximo o painel do Power BI.
- Power BI Desktop (versão mais recente)
- Power BI Pro (se você quiser compartilhar o painel com os outros)

## <a name="reports"></a>Relatórios

Dois relatórios são gerados automaticamente. 

O primeiro relatório mostra uma visão histórica das medições relatadas pelos dispositivos e divide os diferentes tipos de medições e dispositivos que enviaram o maior número de medições.

![Página de relatório do Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

O segundo relatório aprofunda os eventos e mostra uma visão histórica dos erros e avisos relatados. Ele também mostra quais dispositivos estão relatando o maior número de eventos de tudo para cima, bem como especificamente os eventos de erro e eventos de aviso.

![Página de relatório do Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Recursos

Visite o AppSource para obter o modelo de solução do [Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Visite o [repositório do Github](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) para saber mais sobre a arquitetura e estender a solução.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a visualizar seus dados no Power BI, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como gerenciar dispositivos](howto-manage-devices.md)
