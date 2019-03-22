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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076989"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeje o ambiente de versão prévia do Azure Time Series Insights

Este artigo descreve as melhores práticas para planejar e começar a usar a Versão Prévia do Azure Time Series Insights rapidamente.

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

Para começar a usar o Time Series Insights, é bom entender:

* O que acontece ao provisionar um ambiente de versão prévia do Time Series Insights.
* Quais são as propriedades IDs e carimbo de data/hora de série temporal.
* Qual é o novo modelo de série temporal e como criar um próprio.
* Como enviar eventos com eficiência em JSON. 
* As opções de recuperação de desastre empresarial do Time Series Insights.

O Time Series Insights usa um modelo empresarial pago conforme o uso. Para saber mais sobre cobranças e capacidade, confira o [preço do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>O ambiente de versão prévia do Time Series Insights

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Ambiente de versão prévia do Time Series Insights
* Conta do Armazenamento do Azure de Uso Geral V1

Para começar, são necessários três itens adicionais:
 
- Um [modelo de série temporal](./time-series-insights-update-tsm.md) 
- Uma [origem de evento conectada ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Configurar as propriedades de ID e carimbo de data/hora da série temporal

Para criar um novo ambiente do Time Series Insights, selecione uma ID de série temporal, que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

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
* O modo de série temporal, os campos de instância e os eventos incluem somente informações necessárias, como:
  * ID da série temporal
  * Carimbo de data/hora

Para saber mais, confira [Formatar eventos](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Recuperação de desastre de negócios

O Time Series Insights é um serviço de alta disponibilidade que usa redundâncias no nível da região do Azure. Não é necessária uma configuração para usar essas funcionalidades inerentes. A plataforma Microsoft Azure também inclui recursos para ajudá-lo a criar soluções com funcionalidades de recuperação de desastre ou disponibilidade entre regiões. Para fornecer alta disponibilidade global entre regiões para dispositivos ou usuários, aproveite esses recursos de recuperação de desastre do Azure. 

Para saber mais sobre os recursos internos do Azure para BCDR (continuidade dos negócios e recuperação de desastres), confira [Continuidade dos negócios e diretrizes técnicas do Azure](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Para obter diretrizes de arquitetura sobre estratégias para aplicativos do Azure a fim de alcançar alta disponibilidade e recuperação de desastre, confira o artigo em [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> 
>  O Time Series Insights não tem BCDR interno.
> Por padrão, o Armazenamento do Azure, o Hub IoT do Azure e os Hubs de Eventos do Azure têm recuperação interna.

Para saber mais, leia:

* [Redundância do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Alta disponibilidade e recuperação de desastre do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Políticas do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Se você precisar de BCDR, ainda poderá implementar uma estratégia de recuperação. Crie um segundo ambiente do Time Series Insights em um backup de região do Azure. Envie eventos para esse ambiente secundário da origem do evento principal. Use um segundo grupo de consumidores dedicado e as diretrizes BCDR da origem do evento.

Siga estas etapas para criar e usar um ambiente secundário do Time Series Insights.

1. Crie um ambiente na segunda região. Para saber mais, confira [Ambientes do Time Series Insights](./time-series-insights-get-started.md).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento. Conecte essa origem do evento ao novo ambiente. Não se esqueça de designar o segundo grupo de consumidores dedicado. Para saber mais, confira a [Documentação do Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) ou a [Documentação do Hub de Eventos](./time-series-insights-data-access.md).
1. Se a região primária for afetada durante um incidente de desastres, redirecione as operações para o backup do ambiente do Time Series Insights.

> [!IMPORTANT]
> * Pode haver atraso no evento no caso de failover.
> * O failover também pode causar um pico momentâneo no processamento de mensagens, conforme as operações são redirecionadas.
> * Para saber mais, confira [Reduzir a latência no Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, leia:

- [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)
- [Modelagem de dados](./time-series-insights-update-tsm.md)