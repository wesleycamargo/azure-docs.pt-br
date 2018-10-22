---
title: Rastrear mensagens B2B com Azure Log Analytics - Aplicativo Lógico do Azure | Microsoft Docs
description: Rastrear comunicação B2B para sua conta de integração e o Aplicativo Lógico do Azure com o Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 06/19/2018
ms.openlocfilehash: 5bf5385824eb9b711a2fee547c29d24d7ef5a01d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125761"
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Rastrear comunicação B2B com Azure Log Analytics

Depois de configurar a comunicação B2B entre dois processos ou aplicativos de negócios em execução por meio de sua conta de integração, essas entidades poderão trocar mensagens entre si. Para verificar se essas mensagens foram processadas corretamente, é possível rastrear as mensagens AS2, X12 e EDIFACT com o [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Por exemplo, você pode usar essas funcionalidades de acompanhamento baseado na Web para o acompanhamento de mensagens:

* Status e contagem de mensagens
* Status de confirmações
* Correlacionar mensagens com confirmações
* Descrição de erro detalhada para falhas
* Recursos de pesquisa

## <a name="requirements"></a>Requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração configurada com o monitoramento e log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não fez isso, [publique os dados de diagnóstico no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Após atender aos requisitos anteriores, será necessário ter um workspace no Log Analytics. É necessário usar o mesmo workspace para rastrear a comunicação B2B no Log Analytics. 
>  
> Se você não tiver um workspace do Log Analytics, saiba[como criar um workspace do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="add-the-logic-apps-b2b-solution-to-log-analytics"></a>Adicionar a solução de Aplicativos Lógicos B2B ao Log Analytics

Para que o Log Analytics rastreie mensagens B2B para seu aplicativo lógico, será necessário adicionar a solução de **Aplicativos Lógicos B2B** ao Portal do OMS. Saiba mais sobre [adicionar soluções ao Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. No [Portal do Azure](https://portal.azure.com), escolha **Todos os serviços**. Pesquise “log analytics” e, em seguida, escolha **Log Analytics**, conforme mostrado aqui:

   ![Encontrar o Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. Em **Log Analytics**, localize e selecione o workspace do Log Analytics. 

   ![Selecionar o workspace do Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Em **Gerenciamento**, escolha **Visão geral**.

   ![Escolha o portal do Log Analytics](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Depois que a página inicial abrir, escolha **Adicionar** para instalar a solução dos Aplicativos Lógicos B2B.    
   ![Escolha Galeria de Soluções](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

5. Em **Solução de Gerenciamento**, localize e crie a solução **Aplicativos Lógicos B2B**.     
   ![Escolher Aplicativos Lógicos B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Na home page, o bloco **Mensagens dos Aplicativos Lógicos B2B** é exibido. 
   Esse bloco atualiza a contagem de mensagens quando as mensagens B2B são processadas.

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Rastrear status e detalhes da mensagem no Log Analytics

1. Depois que as mensagens B2B forem processadas, você poderá exibir o status e os detalhes delas. Na página de Visão Geral, escolha o bloco **Mensagens dos Aplicativos Lógicos B2B**.

   ![Contagem de mensagens atualizada](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Por padrão, o bloco **Mensagens dos Aplicativos Lógicos B2B** mostra dados com base em um único dia. Para alterar o escopo de dados para um intervalo diferente, escolha o controle de escopo na parte superior da página:
   > 
   > ![Alterar o escopo de dados](media/logic-apps-track-b2b-messages-omsportal/server-filter.png)
   >

2. Após a exibição do painel de status de mensagens, você poderá exibir mais detalhes de um tipo de mensagem específico, que mostra dados com base em um único dia. Escolha o bloco **AS2**, **X12** ou **EDIFACT**.

   ![Exibir status da mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Uma lista de mensagens é exibida para o bloco escolhido. 
   Para saber mais sobre as propriedades de cada tipo de mensagem, consulte estas descrições das propriedades de mensagens:

   * [Propriedades de mensagens AS2](#as2-message-properties)
   * [Propriedades de mensagens X12](#x12-message-properties)
   * [Propriedades de mensagens EDIFACT](#EDIFACT-message-properties)

   Por exemplo, veja abaixo a aparência de uma lista de mensagens AS2:

   ![Exibir mensagens AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para exibir ou exportar as entradas e saídas de mensagens específicas, selecione as mensagens e escolha **Baixar**. Quando solicitado, salve o arquivo .zip no computador local e, em seguida, extraia o arquivo. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se você configurar confirmações, a pasta de mensagens também incluirá arquivos com detalhes de confirmação. 
   Cada pasta de mensagens tem, pelo menos, estes arquivos: 
   
   * Arquivos legíveis por humanos com os detalhes de conteúdo de entrada e de saída
   * Arquivos codificados com as entradas e saídas

   Para cada tipo de mensagem, encontre formatos de nome de pasta e arquivo aqui:

   * [Formatos de nome de pasta e arquivo AS2](#as2-folder-file-names)
   * [Formatos de nome de pasta e arquivo X12](#x12-folder-file-names)
   * [Formatos de nome de pasta e arquivo EDIFACT](#edifact-folder-file-names)

   ![Baixar arquivos de mensagem](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para exibir todas as ações que têm a mesma ID da execução, na página **Pesquisa de Logs**, escolha uma mensagem na lista de mensagens.

   Classifique essas ações por coluna ou pesquise resultados específicos.

   ![Ações com a mesma ID da execução](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para pesquisar resultados com consultas pré-criadas, escolha **Favoritos**.

   * Saiba [como criar consultas adicionando filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou saiba mais sobre [como encontrar dados com pesquisas de logs no Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e os valores que você deseja usar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedade e formatos de nome de mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, estas são as descrições de propriedade e os formatos de nome dos arquivos de mensagem baixados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade da mensagem AS2

Estas são as descrições das propriedades de cada mensagem AS2.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do AS2 |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado em **Configurações de Envio** de um contrato do AS2 |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do AS2 são configuradas |
| Status | O status da mensagem AS2 <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. Nenhum MDN está configurado. <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e é recebido ou o MDN é enviado. <br>Com Falha = recebimento de uma mensagem AS2 inválida. Nenhum MDN está configurado. <br>Pendente = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e o MDN é esperado. |
| Ack | O status da mensagem MDN <br>Aceito = recebimento ou envio de um MDN positivo. <br>Pendente = aguardando recebimento ou envio de um MDN. <br>Rejeitado = recebimento ou envio de um MDN negativo. <br>Não Obrigatório = o MDN não está configurado no contrato. |
| Direção | A direção da mensagem AS2 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| ID da Mensagem | ID da mensagem AS2 dos cabeçalhos da mensagem AS2 |
| Timestamp | A hora em que a ação do AS2 processou a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome AS2 para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos AS2 baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições das propriedades da mensagem X12

Estas são as descrições das propriedades de cada mensagem X12.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do X12 |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do X12 |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do X12 são configuradas |
| Status | O status da mensagem X12 <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem X12 inválida. <br>Pendente = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| Direção | A direção da mensagem X12 |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| Tipo de mensagem | O tipo de mensagem EDI X12 |
| ICN | O Número de Controle de Intercâmbio da mensagem X12 |
| TSCN | O Número de Controle do Conjunto de Transações da mensagem X12 |
| Timestamp | A hora em que a ação do X12 processou a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome X12 para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos X12 baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições das propriedades da mensagem EDIFACT

Estas são as descrições das propriedades de cada mensagem EDIFACT.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Remetente | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do EDIFACT |
| Receptor | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do EDIFACT |
| Aplicativo Lógico | O aplicativo lógico no qual as ações do EDIFACT são configuradas |
| Status | O status da mensagem EDIFACT <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem EDIFACT inválida <br>Pendente = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| Ack | Status da Confirmação Funcional (997) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| Direção | A direção da mensagem EDIFACT |
| ID de Correlação | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| Tipo de mensagem | O tipo da mensagem EDIFACT |
| ICN | O Número de Controle de Intercâmbio da mensagem EDIFACT |
| TSCN | O Número de Controle do Conjunto de Transações da mensagem EDIFACT |
| Timestamp | A hora em que a ação do EDIFACT processou a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para arquivos de mensagem baixados

Estes são os formatos de nome de cada pasta de mensagens e arquivos EDIFACT baixados.

| Pasta ou arquivo | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagens | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Entrada, saída e, se configurado, os arquivos de confirmação | **Conteúdo de entrada**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Conteúdo de saída**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Saídas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Próximas etapas

* [Consulta de mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)