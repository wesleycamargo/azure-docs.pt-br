---
title: "Acompanhar mensagens AS2, X12, EDIFACT usando uma consulta - Aplicativos Lógicos do Azure | Microsoft Docs"
description: Use consultas para acompanhar mensagens B2B no portal do Operations Management Suite
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Acompanhar mensagens B2B no portal do Operations Management Suite por meio de uma consulta
Para controlar mensagens do business-to-business (B2B) no portal do Operations Management Suite, você pode criar uma consulta que filtra os dados para um número de controle de intercâmbio específico.

## <a name="prereqs"></a>Pré-requisitos

Para depuração e para obter mais informações de diagnóstico, ative o diagnóstico em sua [conta de integração](logic-apps-monitor-b2b-message.md) para seus [aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que têm conectores X12. Em seguida, siga as etapas para [publicar dados de diagnóstico](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) no portal do Operations Management Suite.

## <a name="search-for-an-interchange-control-number"></a>Pesquisar por um número de controle de intercâmbio

1. Na página inicial, selecione **Pesquisar Logs**.  
![Selecionar pesquisa de log](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Na caixa de pesquisa, digite **Type="AzureDiagnostics"**. Para filtrar os dados, selecione **Adicionar**.  
![Selecionar consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Digite **interchange**, selecione **event_record_messageProperties_interchangeControlNumber_s** e clique em **Adicionar**.  
![Adicionar filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Selecione o número de controle para o qual você quer filtrar dados e clique em **Aplicar**.  
![Pesquisar o número de controle](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Localize a consulta criada por você para filtrar dados para o número de controle selecionado.   
![Localizar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Digite um nome para a consulta e, em seguida, salve-a.   
![Salvar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Selecione **favoritos** para exibir a consulta e usá-la em pesquisas futuras.  
![Exibir a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. A consulta pode ser atualizada para pesquisar por um novo número de controle de intercâmbio.  
![Atualizar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [esquemas de acompanhamento personalizado](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Saiba mais sobre os [esquemas de acompanhamento AS2](logic-apps-track-integration-account-as2-tracking-schemas.md).    
* Saiba mais sobre os [esquemas de acompanhamento X12](logic-apps-track-integration-account-x12-tracking-schema.md).  
* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO5-->


