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
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Rastreando mensagens B2B no portal do OMS
A comunicação B2B envolve trocas de mensagens entre dois processos de negócios ou aplicativos em execução. O rastreamento de mensagens B2B no portal do OMS oferece recursos de controle avançados e baseados na Web que permitem exibir se as mensagens foram processadas corretamente.  Você pode rastrear

* Contagem e o status de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrição de erro detalhada para falhas
* Recursos de pesquisa

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração; você pode criar uma [Conta Integração](app-service-logic-enterprise-integration-create-integration-account.md) e habilitar o registro em log; você pode encontrar as etapas [aqui](app-service-logic-monitor-b2b-message.md)
* Um Aplicativo Lógico; você pode criar um [Aplicativo Lógico](app-service-logic-create-a-logic-app.md) e habilitar o registro em log; veja as etapas [aqui](app-service-logic-monitor-your-logic-apps.md)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Adição da solução Aplicativos Lógicos B2B ao portal do OMS
1. Selecione **Mais Serviços** no portal, pesquise **log analytics** e selecione **log analytics**
![Pesquisar o log analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selecionar seu **Log Analytics**
![Selecionar o log analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. Selecione **Portal do OMS**, abra a home page do portal do OMS![Navegar no portal do OMS](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. Selecionar **Galeria de Soluções**    
![Selecionar a Galeria de Soluções](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. Selecionar **Aplicativos Lógicos B2B**
![Selecionar Aplicativos Lógicos B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. Clique em **Adicionar** para adicionar **Mensagens dos Aplicativos Lógicos B2B** à home page ![Selecionar Adicionar](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. Navegue na home page para exibir **Mensagens dos Aplicativos Lógicos B2B**
![Selecionar home page](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

8. Pós-processo de mensagem; as atualizações da home page com contagem de mensagens ![Selecionar home page](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

9. A seleção de **Mensagens dos Aplicativos Lógicos B2B** na home page leva aos status de mensagem AS2 e X12.  Os dados se baseados no último dia.
![Selecionar Mensagens dos Aplicativos Lógicos B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. A seleção das mensagens AS2 ou X12 por status leva você para a lista de mensagens ![Selecionar status de mensagem AS2](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![Selecionar status de mensagem X12](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. A seleção de uma linha na lista de mensagens AS2 ou X12 leva você à pesquisa de log.  A pesquisa de log lista todas as ações com a mesma **ID de Execução**
![Selecionar status da mensagem](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)


## <a name="next-steps"></a>Próximas etapas
[Esquema de Controle Personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Esquema de Controle do AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Esquema de Controle do X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


