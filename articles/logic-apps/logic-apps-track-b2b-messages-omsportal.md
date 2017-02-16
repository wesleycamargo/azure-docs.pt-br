---
title: Rastrear mensagens B2B no portal do OMS | Microsoft Docs
description: Como rastrear mensagens B2B no portal do OMS
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
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Rastreando mensagens B2B no portal do OMS
A comunicação B2B envolve trocas de mensagens entre dois processos de negócios ou aplicativos em execução. O acompanhamento de mensagens B2B no portal do OMS oferece funcionalidades de acompanhamento avançadas e baseadas na Web, as quais permitem exibir se as mensagens foram processadas corretamente.  Você pode rastrear

* Contagem e o status de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrição de erro detalhada para falhas
* Recursos de pesquisa

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração; você pode criar uma [Conta Integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e habilitar o registro em log; você pode encontrar as etapas [aqui](logic-apps-monitor-b2b-message.md)
* Um Aplicativo Lógico; você pode criar um [Aplicativo Lógico](../logic-apps/logic-apps-create-a-logic-app.md) e habilitar o registro em log; veja as etapas [aqui](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Adição da solução Aplicativos Lógicos B2B ao portal do OMS

1. Selecione **Mais Serviços** no portal, pesquise **log analytics** e selecione **log analytics**   
![Pesquisar no log analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selecione seu **Log Analytics**  
![Selecionar o log analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Selecione **Portal do OMS**, abra a home page do portal do OMS   
![Navegar no Portal do OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Selecionar **Galeria de Soluções**    
![Selecionar a Galeria de Soluções](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Selecione **Aplicativos Lógicos B2B**     
![Selecionar Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Clique em **Adicionar** para adicionar **Mensagens dos Aplicativos Lógicos B2B** à home page  
![Selecionar Adicionar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. Navegue na home page para exibir **Mensagens dos Aplicativos Lógicos B2B**   
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Acompanhamento de dados no portal do OMS

1. Pós-processo de mensagem; as atualizações da home page com contagem de mensagens   
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. A seleção de **Mensagens dos Aplicativos Lógicos B2B** na home page leva aos status de mensagem AS2 e X12.  Os dados se baseados no último dia.
![Selecionar Mensagens dos Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3.A seleção das mensagens AS2 ou X12 por status leva você para a lista de mensagens   
![Selecionar status de mensagem AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado definido nas configurações de recebimento, ou o parceiro de host definido nas configurações de envio para um contrato AS2 |
| Receptor | O parceiro de host definido nas configurações de recebimento, ou o parceiro convidado definido nas configurações de envio para um contrato AS2 |
| Aplicativo Lógico | Aplicativo Lógico no qual as ações de AS2 estão configuradas |
| Status | Status de mensagem AS2. Sucesso = Recebeu ou enviou uma mensagem AS2 válida, sem MDN configurado; Sucesso = Recebeu ou enviou uma mensagem AS2 válida, MDN configurado e recebeu ou enviou MDN; Falha = Recebeu uma mensagem AS2 inválida, sem MDN configurado; Pendente= Recebeu ou enviou uma mensagem AS2 válida, MDN; configurado e esperando uma confirmação funcional; |
| Ack | Status de mensagem MDN |
| Direção | Direção da mensagem AS2 |
| ID de Correlação | ID para correlacionar todos os gatilhos e ações em um Aplicativo Lógico |
| ID da Mensagem |  ID da mensagem AS2, dos cabeçalhos da mensagem AS2 |
| Timestamp | Hora na qual a ação AS2 processou a mensagem |
|  |  |


![Selecionar status de mensagem X12](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado definido nas configurações de recebimento, ou o parceiro de host definido nas configurações de envio para um contrato AS2 |
| Receptor | O parceiro de host definido nas configurações de recebimento, ou o parceiro convidado definido nas configurações de envio para um contrato AS2 |
| Aplicativo Lógico | Aplicativo Lógico no qual as ações de AS2 estão configuradas |
| Status | Status de mensagem X12. Sucesso = Recebeu ou enviou uma mensagem X12 válida, sem confirmação funcional configurada; Sucesso = Recebeu ou enviou uma mensagem X12 válida, confirmação funcional configurada e recebeu ou enviou confirmação funcional; Falha = Recebeu uma mensagem X12 inválida; Pendente= Recebeu ou enviou uma mensagem X12 válida, confirmação funcional configurada e esperando uma confirmação funcional |
| Ack | Status da confirmação funcional (997).  Aceita = Recebeu ou enviou uma confirmação funcional positiva; Rejeitado = Recebeu ou enviou uma confirmação funcional negativa; Pendente = esperando uma confirmação funcional, mas sem recebê-la; Pendente = Gerou uma confirmação funcional, mas não foi possível enviá-la ao parceiro |
| Direção | Direção da mensagem X12 |
| ID de Correlação | ID para correlacionar todos os gatilhos e ações em um Aplicativo Lógico |
| Tipo de mensagem |  Tipo de Mensagem EDI X12 |
| ICN | Número de controle de intercâmbio da mensagem X12 |
| TSCN | Número de controle de conjunto transacional da mensagem X12 |
| Timestamp | Hora na qual a ação X12 processou a mensagem |
| | |

4.A seleção de uma linha na lista de mensagens AS2 ou X12 leva você à pesquisa de log.  A pesquisa de log lista todas as ações com a mesma **ID de Execução**
![Selecionar status da mensagem](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>Consultas no portal do OMS

Na página de Pesquisa, crie uma consulta e, quando pesquisar, poderá filtrar os resultados usando controles da faceta.

### <a name="how-to-create-a-query"></a>Como criar uma consulta

1. Na pesquisa de log, grave uma consulta e selecione **Salvar**.  Veja [aqui](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md) as etapas para gravar uma consulta ![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **Salvar Pesquisa** é aberto.  Dê um **nome**, **categoria** e clique em **Salvar**   
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Para exibir a consulta, selecione **favoritos**    
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Como usar uma consulta salva

* Na pesquisa de log, selecione **favoritos** para exibir as consultas salvas.  Ao selecionar uma delas, você verá os resultados da consulta ![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Próximas etapas
[Esquema de Controle Personalizado](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Esquema de Controle do AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Esquema de Controle do X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


