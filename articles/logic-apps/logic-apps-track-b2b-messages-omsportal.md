---
title: Rastrear mensagens B2B no portal do Operations Management Suite - Azure | Microsoft Docs
description: Como rastrear mensagens B2B no portal do Operations Management Suite
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
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: d1c61ba220b4334f053428a23e620e8004fc60f7
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Rastrear mensagens B2B no portal do Operations Management Suite

![Símbolo do Aplicativo Log B2B](./media/logic-apps-track-b2b-messages-omsportal/logic-apps-b2b-symbol.png)

A comunicação B2B envolve a troca de mensagens entre dois processos de negócios ou aplicativos em execução. Use os seguintes recursos de controle baseado na web no portal do Operations Management Suite para confirmar se as mensagens forem processadas corretamente:

* Status e contagem de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrição de erro detalhada para falhas
* Recursos de pesquisa

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure. Você pode criar uma [conta gratuita](https://azure.microsoft.com/free).
* Uma conta de integração. Você pode criar um [conta integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e configurar o log. Para configurar o registro em log, consulte [mensagens B2B Monitor](logic-apps-monitor-b2b-message.md).
* Um aplicativo lógico. Você pode criar um [aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) e configurar o log. Para configurar o registro em log, consulte [diagnóstico do Azure e alertas](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Adicionar a solução B2B de Aplicativos Lógicos no portal do Operations Management Suite

1. No portal do Azure, selecione **mais serviços**, procure a análise de log e, em seguida, selecione **Log Analytics**.   
![Pesquisar no log analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selecione seu **Log Analytics**.  
![Selecionar o log analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Selecione **Portal OMS**. O Operations Management Suite portal home page é exibida.   
![Procurar portal Operations Management Suite](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Selecione **Galeria de Soluções**.    
![Selecionar a Galeria de Soluções](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Selecione **Aplicativos Lógicos B2B**.     
![Selecionar Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Selecione **Adicionar** para adicionar mensagens de **Aplicativos Lógicos B2B** à home page.  
![Selecionar Adicionar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **As mensagens dos Aplicativos Lógicos B2B** aparecem na home page.   
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Rastrear dados no portal do Operations Management Suite

1. Depois que as mensagens sejam processadas, a contagem de mensagens atualizado é exibido.   
![Mensagens atualizadas](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Selecione **mensagens B2B dos Aplicativos Lógicos** na home page para exibir as mensagens de status AS2 e X12.  Os dados são baseados em um único dia.
![Selecionar Mensagens dos Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Selecione uma mensagem AS2, X12 ou EDIFACT por status para ir para a lista de mensagens. A próxima captura de tela mostra o status da mensagem AS2. As descrições de propriedade de status de mensagem AS2 e X12 aparecem posteriormente em "Descrições de propriedade de lista de mensagens".  
![Selecionar status de mensagem AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

4. Selecione uma linha na mensagem AS2, X12 ou EDIFACT para ir para a pesquisa de logs.  Lista de pesquisa de log de todas as ações que têm a mesma ID de execução.
![Selecione o status da mensagem](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>Descrições de propriedade da lista de mensagens

#### <a name="as2-message-list-property-descriptions"></a>Descrições de propriedade da lista de mensagens AS2

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado definido nas configurações de recebimento ou o parceiro de host definido nas configurações de envio para um contrato AS2. |
| Receptor | O parceiro de host definido nas configurações de recebimento ou o parceiro convidado definido nas configurações de envio para um contrato AS2. |
| Aplicativo Lógico | O aplicativo Lógico no qual as ações do AS2 estão configuradas. |
| Status | Status de mensagem AS2 <br>Sucesso = Recebeu ou enviou uma mensagem AS2 correta, nenhum MDN está configurado <br>Sucesso = Recebeu ou enviou uma mensagem AS2 correta, o MDN é configurado e recebido ou o MDN é enviado <br>Falha = Recebeu uma mensagem AS2 incorreta, nenhum MDN está configurado <br>Pendente = Recebeu ou enviou uma mensagem AS2 correta, o MDN é configurado e MDN é esperado |
| Ack | Status de mensagem MDN <br>Aceito = Recebeu ou enviou um MDN positivo <br>Pendente = Aguardando para receber ou enviar um MDN <br>Rejeitado = Recebeu ou enviou um MDN negativo <br>Não Obrigatório = MDN não está configurado no contrato |
| Direção | Direção de mensagem AS2. |
| ID de Correlação | ID para correlacionar todos os gatilhos e ações em um Aplicativo lógico. |
| ID da Mensagem |  ID da mensagem AS2, dos cabeçalhos da mensagem AS2. |
| Timestamp | Hora em que a ação AS2 processa a mensagem. |

#### <a name="x12-message-list-property-descriptions"></a>Descrições da lista de propriedade da mensagem X12

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado definido nas configurações de recebimento ou o parceiro de host definido nas configurações de envio para um contrato X12. |
| Receptor | O parceiro de host definido nas configurações de recebimento ou o parceiro convidado definido nas configurações de envio para um contrato X12. |
| Aplicativo Lógico | O aplicativo Lógico no qual as ações do AS2 estão configuradas. |
| Status | Status de mensagem X12 <br>Sucesso = Recebeu ou enviou uma mensagem X12 correta, nenhuma confirmação está configurada <br>Sucesso = Recebeu ou enviou uma mensagem X12 correta, uma confirmação funcional é configurada e recebida ou uma confirmação funcional é enviada <br>Falha = Recebeu ou enviou uma mensagem X12 incorreta <br>Pendente = Recebeu ou enviou uma mensagem X12 correta, a confirmação funcional é configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = Recebeu ou enviou uma confirmação funcional positiva <br>Rejeitado = Recebeu ou enviou uma confirmação funcional negativa <br>Pendente = Esperando uma confirmação funcional, mas não a recebeu <br>Pendente = Gerou uma confirmação funcional, mas não foi possível enviá-la ao parceiro <br>Não obrigatório = Confirmação funcional não está configurada |
| Direção | Direção da mensagem X12. |
| ID de Correlação | ID para correlacionar todos os gatilhos e ações em um Aplicativo lógico. |
| Tipo de mensagem |  Tipo de Mensagem EDI X12. |
| ICN | Número de controle de intercâmbio da mensagem X12. |
| TSCN | Número de controle de conjunto transacional da mensagem X12. |
| Timestamp | Hora na qual a ação X12 processa a mensagem. |


#### <a name="edifact-message-list-property-descriptions"></a>Descrições da lista de propriedade da mensagem EDIFACT

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro convidado definido nas configurações de recebimento ou o parceiro de host definido nas configurações de envio para um contrato EDIFACT. |
| Receptor | O parceiro de host definido nas configurações de recebimento ou o parceiro convidado definido nas configurações de envio para um contrato EDIFACT. |
| Aplicativo Lógico | O aplicativo Lógico no qual as ações do AS2 estão configuradas. |
| Status | Status da mensagem EDIFACT <br>Sucesso = Recebeu ou enviou uma mensagem X12 correta, nenhuma confirmação está configurada <br>Sucesso = Recebeu ou enviou uma mensagem X12 correta, uma confirmação funcional é configurada e recebida ou uma confirmação funcional é enviada <br>Falha = Recebeu ou enviou uma mensagem X12 incorreta <br>Pendente = Recebeu ou enviou uma mensagem X12 correta, a confirmação funcional é configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = Recebeu ou enviou uma confirmação funcional positiva <br>Rejeitado = Recebeu ou enviou uma confirmação funcional negativa <br>Pendente = Esperando uma confirmação funcional, mas não a recebeu <br>Pendente = Gerou uma confirmação funcional, mas não foi possível enviá-la ao parceiro <br>Não obrigatório = Confirmação funcional não está configurada |
| Direção | Direção da mensagem EDIFACT. |
| ID de Correlação | ID para correlacionar todos os gatilhos e ações em um Aplicativo lógico. |
| Tipo de mensagem |  Status da mensagem EDIFACT. |
| ICN | Número de controle de intercâmbio da mensagem EDIFACT. |
| TSCN | Número de controle de conjunto transacional da mensagem EDIFACT. |
| Timestamp | Hora na qual a ação EDIFACT processa a mensagem. |


## <a name="queries-in-the-operations-management-suite-portal"></a>Consultas no portal do Operations Management Suite

Na página de pesquisa, você pode criar uma consulta. Quando você pesquisa, você pode filtrar os resultados por meio de controles da faceta.

### <a name="create-a-query"></a>Criar uma consulta

1. Na pesquisa de log, grave uma consulta e selecione **Salvar**. **Salvar pesquisa** é exibida. Para escrever uma consulta, veja [Rastrear mensagens B2B no portal do Operations Management Suite usando uma consulta](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Em **Salvar pesquisa** adicionar um **nome** e **categoria**e, em seguida, selecione **salvar**.   
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Para exibir a consulta, selecione **favoritos**.    
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>Usar uma consulta salva

* Na pesquisa de log, selecione **favoritos** para exibir as consultas salvas.  Para exibir os resultados da consulta, selecione uma consulta.
![Selecionar home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Próximas etapas
[Esquema de Controle Personalizado](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Esquema de Controle do AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Esquema de Controle do X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

