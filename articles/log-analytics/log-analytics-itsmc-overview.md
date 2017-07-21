---
title: "Conector de Gerenciamento de Serviço de TI no OMS | Microsoft Docs"
description: "Use o Conector de Gerenciamento de Serviço de TI para monitorar e gerenciar os itens de trabalho de ITSM no OMS de forma centralizada e resolver problemas rapidamente."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 54974ef06efdae69ddbfa12b1ba9278b48b113d3
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gerenciar itens de trabalho de ITSM de forma centralizada usando o Conector de Gerenciamento de Serviço de TI (Visualização)

![Símbolo do Conector de Gerenciamento do Serviço de TI](./media/log-analytics-itsmc/itsmc-symbol.png)

Você pode usar o ITSMC (Conector de Gerenciamento de Serviço de TI) no Log Analytics do OMS para monitorar e gerenciar itens de trabalho em todos os produtos e serviços de ITSM de forma centralizada.

O Conector de Gerenciamento do Serviço de TI é integrado aos produtos e serviços de ITSM (Gerenciamento do Serviço de TI) com o Log Analytics do OMS.  A solução tem uma integração bidirecional com produtos/serviços de ITSM, que fornece aos usuários do OMS uma opção para criar incidentes, alertas ou eventos na solução de ITSM. O conector também importa dados, como incidentes e solicitações de alteração, da solução de ITSM para o Log Analytics do OMS.

Com o Conector de Gerenciamento de Serviço de TI, você pode:

  - Monitorar e gerenciar itens de trabalho para produtos/serviços de ITSM usados em sua organização de forma centralizada.
  - Criar itens de trabalho de ITSM (como alerta, evento e incidente) no ITSM com base em alertas do OMS e por meio da pesquisa de logs.
  - Ler incidentes e solicitações de alteração na solução de ITSM e correlacioná-los com os dados de logs relevantes no espaço de trabalho do Log Analytics.
  - Localizar eventos inesperados e incomuns e resolvê-los, mesmo antes de os usuários finais entrarem em contato e relatá-los para a assistência técnica.
  - Importar dados de itens de trabalho para o Log Analytics e criar relatórios de KPI (indicador chave de desempenho).  Com esses relatórios, você pode identificar, avaliar e tomar decisões em relação a vários itens importantes, como avaliação de malware.
  - Exibir painéis estruturados para obter informações mais profundas sobre incidentes, solicitações de alteração e sistemas afetados.
  - Resolver problemas mais rapidamente correlacionando-os com outras soluções de gerenciamento do espaço de trabalho do Log Analytics.


## <a name="prerequisites"></a>Pré-requisitos

Para importar itens de trabalho de ITSM para o Log Analytics do OMS, a solução precisa de uma conexão entre o Conector de Gerenciamento de Serviço de TI no OMS e o produto/serviço de IT SM do qual os itens de trabalho são importados.


## <a name="configuration"></a>Configuração

Adicione a solução Conector de Gerenciamento de Serviço de TI ao espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).

O bloco Conector de Gerenciamento de Serviço de TI como visto na Galeria de soluções:

![bloco do conector](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Após a adição bem-sucedida, você verá o Conector de Gerenciamento de Serviço de TI em **OMS** > **Configurações** > **Fontes Conectadas.**

![ITSMC conectado](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Por padrão, o Conector de Gerenciamento do Serviço de TI atualiza os dados da conexão uma vez em cada 24 horas. Para atualizar os dados da conexão instantaneamente para as edições ou atualizações do modelo que você fizer, clique no botão Atualizar exibido ao lado de sua conexão.

 ![Atualização do ITSMC](./media/log-analytics-itsmc/itsmc-connection-refresh.png)

## <a name="management-packs"></a>Pacotes de gerenciamento
Essa solução não exige nenhum pacote de gerenciamento.

## <a name="connected-sources"></a>Fontes conectadas

Os seguintes produtos/serviços de ITSM têm suporte no Conector de Gerenciamento de Serviço de TI:

- [SCSM (System Center Service Manager)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Usando a solução

Depois de conectar o Conector de Gerenciamento do Serviço de TI do OMS com o serviço de ITSM, os serviços do Log Analytics começam a coletar os dados dos produtos/serviço do ITMS conectado.

> [!NOTE]
> - Os dados importados pelo Conector de Gerenciamento do Serviço de TI são exibidos no Log Analytics como eventos chamados **ServiceDesk_CL**.
- O evento contém um campo chamado **ServiceDeskWorkItemType_s**. que pode usar seu valor como um incidente ou uma solicitação de alteração, dependendo dos dados de item de trabalho contidos no evento **ServiceDesk_CL**.

## <a name="input-data"></a>Dados de entrada
Itens de trabalho importados dos produtos/serviços do ITSM.

As seguintes informações mostram exemplos de dados coletados pelo conector de Gerenciamento de Serviço de TI:

> [!NOTE]
> Dependendo do tipo de item de trabalho importado para o Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Item de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Campos**

- ServiceDeskConnectionName
- ID da Central de Serviços
- Estado
- Urgência
- Impacto
- Prioridade
- Escalonamento
- Criado por
- Resolvido por
- Fechado por
- Fonte
- Atribuído a
- Categoria
- Title
- Descrição
- Data de criação
- Data de fechamento
- Data de resolução
- Data da última modificação
- Computador


**Item de trabalho:** **Solicitações de alteração**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID da Central de Serviços
- Criado por
- Fechado por
- Fonte
- Atribuído a
- Title
- Tipo
- Categoria
- Estado
- Escalonamento
- Status do conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de criação
- Data de fechamento
- Data da última modificação
- Data de solicitação
- Data de início prevista
- Data de término prevista
- Data de início do trabalho
- Data de término do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída de um incidente do ServiceNow

| Campo do OMS | Campo do ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contato |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Descrição breve |
| Description_s|  Observações |
| CreatedDate_t|  Aberto |
| ClosedDate_t| closed|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para uma solicitação de alteração do ServiceNow

| Campo do OMS | Campo do ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Descrição breve |
| Type_s|  Tipo |
| Category_s|  Catgory |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data de fechamento |
| PlannedStartDate_t  |     Data de início planejada |
| PlannedEndDate_t  |   Data de término planejada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de término real|
| Description_s | Descrição |
| Computador  | Item de Configuração |

**Tela de exemplo do Log Analytics para dados de ITSM:**

![Tela do Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>Conector de Gerenciamento de Serviço de TI – integração com outras soluções do OMS

Atualmente, o Conector de Gerenciamento de Serviço de TI dá suporte à integração com a solução Mapa do Serviço.

O Mapa do Serviço descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais. O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente. Mais informações: [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md).

Com essa integração, é possível exibir os itens da central de serviços criados nas soluções de ITSM, conforme mostrado no seguinte exemplo:

![Solução integrada ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Criar itens de trabalho de ITSM para alertas do OMS

Para os alertas do OMS, é possível criar itens de trabalho associados nas fontes de ITSM conectadas.  Para fazer isso, use o seguinte procedimento:

1. Na janela **Pesquisa de Logs**, execute uma consulta da pesquisa de logs para exibir os dados. Os resultados da consulta são a fonte de itens de trabalho.
2. Em **Pesquisa de Logs**, clique em **Alerta** para abrir a página **Adicionar Regra de Alerta**.

    ![Tela do Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Na janela **Adicionar Regra de Alerta**, forneça os detalhes necessários para **Nome**, **Gravidade**, **Consulta de pesquisa** e **Critérios de alerta** (medição Janela de Tempo/Métrica).
4. Selecione **Sim** para **Ações de ITSM**.
5. Selecione a conexão de ITSM na lista **Selecionar Conexão**.
6. Forneça os detalhes conforme necessário.
7. Para criar um item de trabalho separado para cada entrada de log desse alerta, marque a caixa de seleção **Criar itens de trabalho individuais para cada entrada de log**.

    Ou

    deixe essa caixa de seleção desmarcada para criar apenas um item de trabalho para qualquer quantidade de entradas de log nesse alerta.

7. Clique em **Salvar**.

O alerta do OMS será criado em **Alertas**. Os itens de trabalho da conexão de ITSM correspondentes são criados quando o critério do alerta especificado é atendido.

## <a name="create-itsm-work-items-from-oms-logs"></a>Criar itens de trabalho de ITSM com base em logs do OMS

É possível criar itens de trabalho nas fontes de ITSM conectadas usando a Pesquisa de Logs do OMS. Para fazer isso, use o seguinte procedimento:

1. Na **Pesquisa de Logs**, pesquise os dados necessários, selecione os detalhes e clique em **Criar item de trabalho**.

    A janela **Criar item de trabalho de ITSM** é exibida:

    ![Tela do Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Adicione os seguintes detalhes:

  - **Título do Item de trabalho**: título do item de trabalho.
  - **Descrição do Item de trabalho**: descrição do novo item de trabalho.
  - **Computador Afetado**: nome do computador no qual esses dados de logs foram encontrados.
  - **Selecionar Conexão**: conexão de ITSM na qual você deseja criar esse item de trabalho.
  - **Item de trabalho**: tipo de item de trabalho.

3. Para usar um modelo de item de trabalho existente para um incidente, clique em **Sim** na opção **Gerar item de trabalho com base no modelo** e, em seguida, clique em **Criar**.

    Ou,

    Clique em **Não** se desejar fornecer valores personalizados.

4. Forneça os valores apropriados nas caixas de texto **Tipo de Contato**, **Impacto**, **Urgência**, **Categoria** e **Subcategoria** e clique em **Criar**.

O item de trabalho será criado no ITSM, que também pode ser exibido no OMS.

## <a name="troubleshoot-itsm-connections-in-oms"></a>Solucionar problemas de conexões de ITSM no OMS
1.  Se houver falha da conexão da interface do usuário da fonte conectada e você receber a mensagem **Erro ao salvar a conexão**, faça o seguinte:
 - No caso de conexões do ServiceNow, do Cherwell e do Provance, verifique se você digitou corretamente o nome de usuário/senha e ID do cliente/segredo do cliente para cada uma das conexões. Se o erro persistir, verifique se você tem privilégios suficientes no produto ITSM correspondente para fazer a conexão.
 - No caso do Service Manager, certifique-se de que o aplicativo Web está implantado com êxito e conexão híbrida está criada. Para verificar se a conexão está estabelecida com êxito com o computador do Service Manager local, visite a URL do aplicativo Web conforme detalhado na documentação para fazer a [conexão híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

2.  Se os dados do ServiceNow não estiverem sendo sincronizados no OMS, certifique-se de que a instância do ServiceNow não está em suspensão. Isso pode acontecer em algum momento nas instâncias de desenvolvimento do ServiceNow, quando estão ociosas. Caso contrário, relate o problema.
3.  Se os alertas estiverem sendo disparados do OMS, mas os itens de trabalho não estiverem sendo criados no produto ITSM ou os itens de configuração não estiverem sendo criados/vinculados aos itens de trabalho ou para qualquer informação genérica, faça o seguinte:
 -  A solução do Conector de Gerenciamento do Serviço de TI no portal do OMS poderia ser usada para obter um resumo das conexões/itens de trabalho/computadores etc. Clique na mensagem de erro na folha de status, navegue até **Pesquisa de Logs** e exiba a conexão com o erro usando os detalhes na mensagem de erro.
 - você pode exibir diretamente os erros/informações relacionadas na página **Pesquisa de Logs** usando *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas de implantação do aplicativo Web do Service Manager
1.  No caso de problemas com a implantação do aplicativo Web, verifique se que você tem permissões suficientes na assinatura mencionada para criar/implantar recursos.
2.  Se a mensagem de erro **Object reference not set to instance of an object (Referência do objeto não definida para a instância de um objeto)** aparecer ao executar o [script](log-analytics-itsmc-service-manager-script.md), certifique-se de ter inserido valores válidos na seção **Configuração do Usuário**.
3.  Se você não conseguir criar o namespace de retransmissão do barramento de serviço, certifique-se de que o provedor de recursos necessário está registrado na assinatura. Se não estiver registrado, crie-o manualmente no Portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) do Portal do Azure.


## <a name="contact-us"></a>Fale conosco

Em caso de dúvidas ou comentários sobre o Conector de Gerenciamento de Serviço de TI, entre em contato conosco pelo email [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Próximas etapas
[Adicionar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-connections.md).

