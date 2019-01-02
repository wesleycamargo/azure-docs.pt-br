---
title: Gerenciamento de Versão Prévia do Time Series Insights – Como provisionar e gerenciar a Versão Prévia do Azure Time Series Insights. | Microsoft Docs
description: Noções básicas sobre como provisionar e gerenciar a Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273487"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Como provisionar e gerenciar a Versão Prévia do Azure Time Series Insights

Este documento descreve como criar e gerenciar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Visão geral

Uma breve visão geral de como provisionar um ambiente de Versão Prévia do Azure Time Series Insights é descrita abaixo:

* Ambientes de Versão Prévia do Azure Time Series Insights são ambientes **PAYG**.
  * Como parte do processo de criação, será necessário fornecer uma ID de Série Temporal. IDs de Série Temporal podem ter de até três chaves. Saiba mais sobre como selecionar uma ID de Série Temporal lendo [Como escolher uma ID de Série Temporal](./time-series-insights-update-how-to-id.md).
  * Ao provisionar um ambiente de versão prévia do Azure Time Series Insights, é possível criar dois recursos do Azure:

    * Um ambiente de Versão Prévia do Azure Time Series Insights  
    * Uma conta de Uso Geral V1 do Armazenamento do Azure
  
    Saiba mais sobre [Como planejar seu ambiente](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > Para clientes que usam contas V2, não habilitar propriedades de arquivamento/a frio na conta de armazenamento que você vai usar.

* Cada ambiente de Versão Prévia do Azure Time Series Insights pode ser associado (opcionalmente) a origem do evento. Leia [como adicionar uma fonte de Hub de Eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Como adicionar uma origem do Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) para obter mais informações.
  * Você fornecerá uma propriedade de ID de Carimbo de Data/Hora e um grupo de consumidores exclusivo durante esta etapa. Fazer isso garante que o ambiente tenha acesso aos eventos apropriados.

* Depois que o provisionamento estiver concluído, você poderá modificar a suas políticas de acesso e outros atributos de ambiente conforme seus requisitos empresariais.

## <a name="new-environment-creation"></a>Criação do novo ambiente

As etapas a seguir descrevem como criar um ambiente de Versão Prévia do Azure Time Series Insights:

1. Selecione o botão **PAYG** no menu **SKU**. Forneça um nome de ambiente, designe qual grupo de assinatura e qual grupo de recursos usar. Em seguida, selecione uma localização com suporte na qual hospedar o ambiente.

1. Insira uma ID de Série Temporal

    >[!NOTE]
    > * A ID de Série Temporal é imutável e diferencia maiúsculas de minúsculas (não pode ser alterada depois de definida).
    > * IDs de série temporal podem ter até 3 chaves.
    > * Leia mais sobre como selecionar uma ID de Série Temporal lendo [Como escolher uma ID de Série Temporal](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de Armazenamento do Azure, selecionando um nome de conta de Armazenamento do Azure e designando uma opção de replicação. Fazer isso automaticamente cria uma conta V1 de uso geral do Armazenamento do Azure. Ela será criada na mesma região que o ambiente de Versão Prévia do Azure Time Series Insights que você selecionou anteriormente.

    ![Criar uma instância do Azure Time Series Insights.][1]

1. Opcionalmente, você pode adicionar uma origem do evento.

   * O Time Series Insights é compatível com o [Hub IoT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e com [Hubs de Eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções. Embora você só possa adicionar uma única origem de evento no momento da criação de ambiente, pode adicionar uma origem de evento adicional mais tarde. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos sejam visíveis para a sua instância da Versão Prévia do Azure Time Series Insights. Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento.

   * Você também deve designar a propriedade de Carimbo de Data/Hora apropriada. Por padrão, o Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a melhor configuração definida para uso em cenários de upload de dados históricos ou de eventos em lote. Verifique sua decisão de usar ou não uma propriedade de Carimbo de Data/Hora nesses casos.

    ![Adicione uma origem de evento à sua instância.][2]

1. Examinar e criar

    ![Adicione uma origem de evento à sua instância.][3]

Confirme que seu ambiente foi provisionado com as configurações desejadas.

## <a name="management"></a>Gerenciamento

Você tem pode gerenciar seu ambiente de Versão Prévia do Azure Time Series Insights usando o portal do Azure. Aqui estão as principais diferenças de gerenciar ambiente de Versão Prévia do Azure Time Series Insights **PAYG** em comparação a um ambiente S1 ou S2 usando o portal do Azure:

* A folha *Visão Geral* do portal do Azure permanece inalterada no Azure Time Series Insights, exceto das seguintes maneiras:
  * Capacidade é removida quando esse conceito não é relevante para ambientes **PAYG**.
  * A propriedade de ID de Série Temporal foi adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da sua conta de Armazenamento do Azure é listado.

* A folha *Configurar* do portal do Azure foi removida na Versão Prévia do Azure Time Series Insights, já que há ambientes **PAYG** não configuráveis.

* A folha de dados de *Referência* do portal do Azure foi removida na Versão Prévia do Azure Time Series Insights, pois os dados de referência não são um componente de ambientes **PAYG**.

![Verifique sua instância.][4]

## <a name="next-steps"></a>Próximas etapas

Leia [Como planejar seu ambiente](./time-series-insights-update-plan.md).

Leia [Como adicionar uma fonte de Hub de Eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Leia [Como adicionar uma fonte de Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
