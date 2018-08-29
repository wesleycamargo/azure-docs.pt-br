---
title: Monitorar o Azure Data Factory com o OMS | Microsoft Docs
description: Saiba como monitorar o Azure Data Factory encaminhando os dados para o Operations Management Suite (OMS) para análise.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42139997"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitorar o Azure Data Factory com o OMS (Operations Management Suite)

Use a integração do Azure Data Factory com o Azure Monitor para encaminhar dados ao OMS (Operations Management Suite). Essa integração é útil nos seguintes cenários:

1.  Você quer escrever consultas complexas em um conjunto avançado de métricas publicadas pelo Data Factory no OMS. Você também pode criar alertas personalizados nessas consultas por meio do OMS.

2.  Você quer monitorar os data factories. Você pode encaminhar os dados de diversos data factories para um único espaço de trabalho do OMS.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Introdução

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Definir as configurações de diagnóstico e de espaço de trabalho do OMS

Habilite as configurações de diagnóstico para seu data factory.

1.  Selecione **Azure Monitor** -> **Configurações de diagnóstico** -> Selecione o data factory -> Ativar diagnósticos.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Fornece as configurações de diagnóstico, incluindo a configuração do espaço de trabalho do OMS.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Instale o pacote Azure Data Factory Analytics OMS pelo Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Clique em **Criar** e selecione o Espaço de Trabalho do OMS e suas configurações.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitorar as métricas do Azure Data Factory usando OMS

A instalação do pacote **Azure Data Factory Analytics** OMS cria um conjunto padrão de exibições que permite as seguintes métricas:

- Execuções do ADF – 1) Execuções de pipeline por Data Factory

- Execuções do ADF – 2) Execuções de atividade por Data Factory

- Execuções do ADF – 3) Execuções de gatilho por Data Factory

- Erros do ADF – 1) 10 principais erros de pipeline por Data Factory

- Erros do ADF – 2) 10 principais execuções de atividade por Data Factory

- Erros do ADF – 3) 10 principais erros de gatilho por Data Factory

- Estatísticas do ADF – 1) Execuções de atividade por Tipo

- Estatísticas do ADF – 2) Execuções de gatilho por Tipo

- Estatísticas do ADF – 3) Duração máx. de execuções de pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Você pode visualizar as métricas acima, examinar as consultas por trás dessas métricas, editar as consultas, criar alertas e assim por diante.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Próximas etapas

Veja [Monitorar e gerenciar os pipelines programaticamente](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) para saber mais sobre o monitoramento e o gerenciamento de pipelines ao executar scripts.
