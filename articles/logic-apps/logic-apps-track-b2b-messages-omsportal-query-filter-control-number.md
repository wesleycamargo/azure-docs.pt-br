---
title: Consulta de mensagens B2B no Log Analytics - Aplicativo Lógico do Azure | Microsoft Docs
description: Criar consultas para rastrear mensagens AS2, X12 e EDIFACT no Log Analytics
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 48cca9919bd09906bdcc3faaaef186ec109c9169
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294302"
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Consulta para mensagens AS2, X12 e EDIFACT no Log Analytics

Para localizar as mensagens AS2, X12 ou EDIFACT que você está rastreando com o [Azure Log Analytics](../log-analytics/log-analytics-overview.md), você pode criar consultas que filtram ações com base em critérios específicos. Por exemplo, você pode encontrar mensagens baseado em um número de controle de intercâmbio específico.

## <a name="requirements"></a>Requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração configurada com o monitoramento e log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se ainda não o fez, [publique os dados de diagnóstico no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configure o rastreamento de mensagens no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Após atender aos requisitos anteriores, será necessário ter um espaço de trabalho no Log Analytics. É necessário usar o mesmo espaço de trabalho para rastrear a comunicação B2B no Log Analytics. 
>  
> Se você não tiver um espaço de trabalho do Log Analytics, saiba[como criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Criar consultas de mensagem com filtros no Log Analytics

Este exemplo mostra como você pode encontrar mensagens com base no número de controle de intercâmbio.

> [!TIP] 
> Se você souber o nome do espaço de trabalho do Log Analytics, vá para a home page do espaço de trabalho (`https://{your-workspace-name}.portal.mms.microsoft.com`) e inicie na etapa 4. Caso contrário, comece na etapa 1.

1. No [Portal do Azure](https://portal.azure.com), escolha **Todos os serviços**. Pesquise “log analytics” e, em seguida, escolha **Log Analytics**, conforme mostrado aqui:

   ![Encontrar o Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Em **Log Analytics**, localize e selecione o espaço de trabalho do Log Analytics.

   ![Selecionar o espaço de trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Em **Gerenciamento**, escolha **Pesquisa de Logs**.

   ![Escolher Pesquisa de Logs](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. Na caixa de pesquisa, insira um campo que você deseja encontrar e, em seguida, pressione **Enter**. Quando você começa a digitar, o Log Analytics mostrará possíveis correspondências e operações que poderão ser utilizadas. Saiba mais sobre [como encontrar dados no Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Este exemplo pesquisa eventos com **Type=AzureDiagnostics**.

   ![Começar a digitar a cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. Na barra à esquerda, escolha o período de tempo que você deseja exibir. Para adicionar um filtro à consulta, escolha **+Adicionar**.

   ![Adicionar um filtro à consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. Em **Adicionar Filtros**, insira o nome do filtro para encontrar o filtro desejado. Selecione o filtro e escolha **+Adicionar**.

   Para encontrar o número de controle de intercâmbio, este exemplo pesquisa a palavra “intercâmbio” e seleciona **event_record_messageProperties_interchangeControlNumber_s** como o filtro.

   ![Selecionar filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. Na barra à esquerda, selecione o valor de filtro que você deseja usar e escolha **Aplicar**.

   Este exemplo seleciona o número de controle de intercâmbio para as mensagens desejadas.

   ![Selecionar o valor do filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Agora, retorne à consulta que está sendo criada. A consulta foi atualizada com o evento de filtro e o valor selecionado. Os resultados anteriores agora são filtrados também.

    ![Retornar à consulta com os resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Salvar a consulta para uso futuro

1. Na consulta, na página **Pesquisa de Logs**, escolha **Salvar**. Dê um nome à consulta, selecione uma categoria e escolha **Salvar**.

   ![Dê um nome à consulta e uma categoria](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Para exibir a consulta, escolha **Favoritos**.

   ![Escolher “Favoritos”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Em **Pesquisas Salvas**, selecione a consulta, de modo que você possa exibir os resultados. Para atualizar a consulta, de modo que você possa encontrar resultados diferentes, edite-a.

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Localizar e executar consultas salvas no Log Analytics

1. Abra a página inicial do espaço de trabalho do Log Analytics (`https://{your-workspace-name}.portal.mms.microsoft.com`) e escolha **Pesquisa de Logs**.

   ![Na home page do Log Analytics, escolha "Pesquisa de Logs"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![No menu, escolha "Pesquisa de Logs"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na home page da **Pesquisa de Logs**, escolha **Favoritos**.

   ![Escolher “Favoritos”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Em **Pesquisas Salvas**, selecione a consulta, de modo que você possa exibir os resultados. Para atualizar a consulta, de modo que você possa encontrar resultados diferentes, edite-a.

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Próximas etapas

* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)