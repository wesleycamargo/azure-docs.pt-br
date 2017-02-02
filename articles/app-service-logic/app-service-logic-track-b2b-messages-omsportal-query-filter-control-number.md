---
title: Rastrear mensagens B2B no portal do Operations Management Suite usando uma consulta | Microsoft Docs
description: Saiba mais sobre acompanhamento de mensagens B2B no portal do Operations Management Suite por meio de uma consulta.
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 684ab31903369070a235b35eb0b8c9adffd309e7
ms.openlocfilehash: 08689b8f57d5b29a47b98eac812eaee285cd13a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Acompanhar mensagens B2B no portal do Operations Management Suite por meio de uma consulta
Para controlar mensagens do business-to-business (B2B) no portal do Operations Management Suite, você pode criar uma consulta que filtra os dados para um número de controle de intercâmbio específico.

## <a name="prerequisites"></a>Pré-requisitos

Para depuração e para obter mais informações de diagnóstico, ative o diagnóstico em sua [conta de integração](app-service-logic-monitor-b2b-message.md) para seus [aplicativos lógicos](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que têm conectores X12. Em seguida, siga as etapas para [publicar dados de diagnóstico](app-service-logic-track-b2b-messages-omsportal.md) no portal do Operations Management Suite.

## <a name="to-create-a-query-to-search-for-a-specific-interchange-control-number"></a>Para criar uma consulta para pesquisar um número específico de controle de intercâmbio

1. Na página inicial, selecione **Pesquisar Logs**.  
![Selecionar pesquisa de log](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Na caixa de pesquisa, digite **Type="AzureDiagnostics"**. Para filtrar os dados, selecione **Adicionar**.  
![Selecionar consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Digite **interchange**, selecione **event_record_messageProperties_interchangeControlNumber_s** e clique em **Adicionar**.  
![Adicionar filtro](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Selecione o número de controle para o qual você quer filtrar dados e clique em **Aplicar**.  
![Pesquisar o número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Localize a consulta criada por você para filtrar dados para o número de controle selecionado.   
![Localizar a consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Digite um nome para a consulta e, em seguida, salve-a.   
![Salvar a consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Selecione **favoritos** para exibir a consulta e usá-la em pesquisas futuras.  
![Exibir a consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. A consulta pode ser atualizada para pesquisar por um novo número de controle de intercâmbio.  
![Atualizar a consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [esquemas de acompanhamento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Saiba mais sobre os [esquemas de acompanhamento AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md).    
* Saiba mais sobre os [esquemas de acompanhamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).  
* Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).



<!--HONumber=Dec16_HO3-->


