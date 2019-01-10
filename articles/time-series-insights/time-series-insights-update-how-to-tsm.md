---
title: Modelagem de dados na Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entenda a modelagem de dados na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555397"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na Versão Prévia do Azure Time Series Insights

Este documento descreve como trabalhar com Modelos de Série Temporal seguindo a Versão Prévia do Azure Time Series Insights. Detalha vários cenários comuns de dados.

Para saber mais sobre como usar a atualização, leia [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Tipos** no menu. Recolha o painel para se concentrar nos tipos de Modelos de Série Temporal.

    ![Portal_one][1]

1. Selecione **Adicionar**.
1. Insira todos os detalhes referentes a tipos e selecione **Criar**. Essa ação cria tipos no ambiente.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de tipo.
1. Escolha **Carregar**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Editar um único tipo

Selecione o tipo e selecione **Editar**. Faça as alterações necessárias e selecione **Salvar**.

![Portal_four][4]

### <a name="delete-a-type"></a>Excluir um tipo

Selecione o tipo e selecione **Excluir**. Se nenhuma instância estiver associada aos tipos, eles serão excluídos.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Hierarquias** no menu. Recolha o painel para se concentrar nas hierarquias de Modelos de Série Temporal.

    ![Portal_six][6]

1. Selecione **Adicionar**.

    ![Portal_seven][7]

1. Selecione **Adicionar Nível** no painel direito.

    ![Portal_eight][8]

1. Insira os detalhes de hierarquia e selecione **Criar**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de hierarquia.
1. Escolha **Carregar**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

Selecione a hierarquia e selecione **Editar**. Faça as alterações necessárias e selecione **Salvar**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

Selecione a hierarquia e selecione **Excluir**. Se nenhuma instância estiver associada à hierarquia, ela será excluída.

![Portal_twelve][12]

## <a name="instances"></a>Instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Instâncias** no menu. Recolha o painel para se concentrar nas instâncias de Modelos de Série Temporal.

    ![Portal_thirteen][13]

1. Selecione **Adicionar**.

    ![Portal_fourteen][14]

1. Insira os detalhes da instância, selecione a associação de tipo e hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo das instâncias.

    ![Portal_fifteen][15]

1. Escolha **Carregar**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

Selecione a instância e selecione **Editar**. Faça as alterações necessárias e selecione **Salvar**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Excluir uma instância

Selecione a instância e selecione **Excluir**. Se nenhum evento estiver associado às instâncias, ele será excluído.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre Modelos de Série Temporal, leia [Modelagem de dados](./time-series-insights-update-tsm.md).
- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
- Para saber mais sobre formas JSON com suporte, leia [Formas JSON com suporte](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png