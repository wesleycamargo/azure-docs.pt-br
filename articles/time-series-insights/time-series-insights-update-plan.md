---
title: Planejar seu ambiente da Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Planeje o ambiente de versão prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: b3fab86b2b2f0ad892e02cd089dbd7c45ce601d6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205765"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeje o ambiente de versão prévia do Azure Time Series Insights

Este artigo descreve as melhores práticas para planejar e começar a usar a Versão Prévia do Azure Time Series Insights rapidamente.

> [!NOTE]
> Ver [planejar seu ambiente Azure tempo série Insights GA](time-series-insights-environment-planning.md), para as práticas recomendadas planejar uma instância TSI de disponibilidade geral.

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

Para começar a usar o Time Series Insights, é bom entender:

* O que você obtém quando você [provisionar um ambiente de visualização de Insights de série de tempo](#the-preview-environment).
* O que seu [propriedades de IDs de série de tempo e o carimbo de hora são](#configure-time-series-ids-and-timestamp-properties).
* Nova [modelo de série temporal é](#understand-the-time-series-model)e como criar seus próprios.
* Como [enviar eventos com eficiência em JSON](#shape-your-events).
* Time Series Insights [opções de recuperação de desastres de negócios](#business-disaster-recovery).

Azure Time Series Insights emprega um modelo pago conforme o uso de negócios. Para saber mais sobre cobranças e capacidade, confira o [preço do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de visualização

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights
* Uma conta de Uso Geral V1 do Armazenamento do Azure

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md).
* Uma [conectado de origem do evento para análise de séries temporais](./time-series-insights-how-to-add-an-event-source-iothub.md).
* [Os eventos estão fluindo para a origem do evento](./time-series-insights-send-events.md) que são mapeados para o modelo e estão no formato JSON válido.

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar propriedades de IDs de série de tempo e carimbo de data

Para criar um novo ambiente do Time Series Insights, selecione uma **ID da série de tempo**. que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> As IDs de série tempo são *imutáveis* e *não podem ser alteradas posteriormente*. Verifique cada uma antes da seleção final e do primeiro uso.

É possível selecionar até três (3) chaves para diferenciar os recursos exclusivamente. Para saber mais, leia [Melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md) e [Armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

A propriedade carimbo de data/hora também é importante. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das origens de evento.

Quando deixado em branco, o Tempo de Enfileiramento do Evento de uma origem do evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, personalizar a propriedade carimbo de data/hora será mais útil que o padrão Tempo de Enfileiramento do Evento. Para saber mais, leia sobre [Como adicionar origens de eventos ao Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora, é possível configurar o modelo de série temporal do ambiente do Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. O modelo usa **IDs de série temporal**, que mapeiam para uma instância que associa o recurso exclusivo com variáveis, conhecidas como tipos e hierarquias. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e envie-o por upload antes de efetuar push dos dados para o Time Series Insights. Para criar seu modelo, confira [Usar o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

É possível verificar a maneira como você envia eventos para o Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Armazenar metadados no modelo de série temporal
* Modo de série de tempo, os campos de instância e eventos incluem somente as informações necessárias, como um **ID da série de tempo** ou **Timestamp**.

Para saber mais, confira [Formatar eventos](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na visualização de Insights de série do tempo.

- Saiba mais sobre [modelagem de dados](./time-series-insights-update-tsm.md) na visualização de Insights de série do tempo.