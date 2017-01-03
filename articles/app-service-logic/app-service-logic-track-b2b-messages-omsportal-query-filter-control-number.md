---
title: Rastrear mensagens B2B no portal do OMS | Microsoft Docs
description: Como rastrear mensagens B2B no portal do OMS
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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>Criar uma consulta no portal do OMS 
Etapas para criar uma consulta que filtra os dados para um número específico de controle de intercâmbio

## <a name="prerequisites"></a>Pré-requisitos

Habilite o diagnóstico para sua [Conta de Integração](app-service-logic-monitor-b2b-message.md), os [aplicativos lógicos](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que têm conectores X12, para obter detalhes mais ricos e depuração.  Siga [estas](app-service-logic-track-b2b-messages-omsportal.md) etapas para publicar dados de diagnóstico no portal do OMS.

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>Criar uma consulta para pesquisar dados de um número específico de controle de intercâmbio

1. Selecionando a **Pesquisa de Log** na home page  
![Selecionar pesquisa de log](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Digite **Type="AzureDiagnostics"** na janela de pesquisa para efetuar pull de todos os dados.  Clique em **Adicionar** para filtrar os dados  
![Selecionar consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Digite **intercâmbio**, selecione **event_record_messageProperties_interchangeControlNumber_s** e clique em **Adicionar**  
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Selecione o número de controle para o qual você quer filtrar dados e clique em **Aplicar**  
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Você pode encontrar a consulta criada para filtrar dados para o número de controle selecionado   
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Dê um nome para a consulta e salve-a   
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. Selecione **favoritos** para exibir a consulta e usar em pesquisas futuras  
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. A consulta pode ser atualizada para procurar um novo número de controle de intercâmbio  
![Selecionar número de controle](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>Próximas etapas
[Esquema de Controle Personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Esquema de Controle do AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Esquema de Controle do X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


