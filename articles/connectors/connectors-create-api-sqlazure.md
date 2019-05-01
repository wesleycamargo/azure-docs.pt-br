---
title: Conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como acessar e gerenciar bancos de dados SQL locais ou de nuvem automatizando fluxos de trabalho com os Aplicativos Lógicos do Azure
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 998fcba50636cd92b14bdbe1633c2548e84a6bfc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696411"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure dos Aplicativos Lógicos do Azure

Este artigo mostra como você pode acessar dados no banco de dados SQL de dentro de um aplicativo lógico com o conector do SQL Server. Dessa forma, você pode automatizar tarefas, processos e fluxos de trabalho que gerenciam dados e recursos SQL com a criação de aplicativos lógicos. O conector funciona tanto para [SQL Server local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) quanto para [Banco de Dados SQL do Azure na nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Você pode criar Aplicativos Lógicos que são executados quando disparados por eventos no Banco de Dados SQL ou em outros sistemas, como Dynamics CRM Online. Seus aplicativos lógicos também podem obter, inserir e excluir dados, além de executar consultas SQL e procedimentos armazenados. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros Dynamics CRM Online, adiciona itens ao banco de dados SQL para quaisquer novos registros e envia alertas de email.

Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/sql/" target="blank">Referência do conector do SQL Server</a>.

## <a name="prerequisites"></a>Pré-requisitos

* O aplicativo lógico em que você precisa de acesso ao banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Um [banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) ou um [banco de dados do SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  As tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um Banco de Dados SQL do Azure, poderá usar bancos de dados de exemplo, que estão incluídos. 

* O nome do SQL Server, o nome do banco de dados, o nome de usuário e a senha. Você precisará dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server. 

  * Para o Banco de Dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou no portal do Azure abaixo das propriedades do Banco de Dados SQL:

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * Para o SQL Server, você pode encontrar esses detalhes na cadeia de conexão: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Antes de poder conectar Aplicativos Lógicos a sistemas locais, como o SQL Server, você deve [configurar um gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode selecionar o gateway ao criar a conexão SQL para seu aplicativo lógico.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Adicionar o gatilho SQL

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "sql server" como filtro. Na lista de gatilhos, selecione o gatilho SQL desejado. 

   Neste exemplo, selecione este gatilho: **SQL Server - quando um item é criado.**

   ![Selecione o gatilho “SQL Server – Quando um item é criado”](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Se forem solicitados os detalhes da conexão, [crie sua conexão SQL agora](#create-connection). 
   Ou, se a conexão já existir, selecione o **Nome de tabela** desejado na lista.

   ![Selecionar tabela](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Defina as propriedades **Intervalo** e **Frequência**, que especificam com que frequência o aplicativo lógico verifica a tabela.

   Este exemplo verifica apenas a tabela selecionada, nada mais. 
   Para fazer algo mais interessante, adicione as ações que executam as tarefas desejadas. 
   
   Por exemplo, para exibir o novo item na tabela, você pode adicionar outras ações, como criar um arquivo que tem campos da tabela e, em seguida, enviar alertas por email. 
   Para saber mais sobre outras ações para esse conector ou outros conectores, consulte [Conectores de Aplicativos Lógicos](../connectors/apis-list.md).

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer. 

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Adicionar ação de SQL

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico é iniciado com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md) e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

2. No Designer de Aplicativo Lógico, no gatilho ou ação, escolha **Nova etapa** > **Adicionar uma ação**.

   ![Escolha “Nova etapa”, “Adicionar uma ação”](./media/connectors-create-api-sqlazure/add-action.png)
   
   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, escolha **Adicionar uma ação**.

2. Na caixa de pesquisa, insira "sql server" como filtro. Na lista de ações, selecione qualquer ação SQL que desejar. 

   Neste exemplo, selecione esta ação, que obtém um único registro: **SQL Server - obter linha**

   ![Insira "sql server", selecione "SQL Server – Obter linha"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Se forem solicitados os detalhes da conexão, [crie sua conexão SQL agora](#create-connection). 
   Ou, se sua conexão existir, selecione um **nome de tabela** e insira a **ID da Linha** do registro desejado.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Este exemplo retorna apenas uma linha da tabela selecionada, nada mais. 
   Para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo com campos da linha para revisão posterior e armazenar o arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações nesse conector ou outros conectores, consulte [Conectores de Aplicativos Lógicos](../connectors/apis-list.md).

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Manipulação de dados em massa

Às vezes, você talvez precise trabalhar com conjuntos de resultados muito grandes, o que o conector não retorna todos os resultados ao mesmo tempo, ou você deseja maior controle sobre o tamanho e a estrutura para seus conjuntos de resultados. Eis algumas maneiras que você pode lidar com esses grandes conjuntos de resultados:

* Para ajudá-lo a gerenciar os resultados como conjuntos menores, ative *paginação*. Para obter mais informações, consulte [obter dados em massa, registros e itens usando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organiza os resultados da maneira desejada.

  Ao obter ou inserindo várias linhas, seu aplicativo lógico pode iterar por meio dessas linhas usando um [ *loop until* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nesses [limites](../logic-apps/logic-apps-limits-and-config.md). 
  No entanto, quando seu aplicativo lógico tem que trabalhar com conjuntos de registros tão grandes, por exemplo, milhares ou milhões de linhas, que você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira que desejar, você pode criar uma [ *procedimento armazenado* ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado em sua instância do SQL e usa o **SELECT – ORDER BY** instrução. 
  Essa solução fornece a você mais controle sobre o tamanho e a estrutura de seus resultados. 
  Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector do SQL Server.

  Para obter detalhes de solução, consulte estes artigos:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginação de SQL para transferência de dados em massa com Aplicativos Lógicos)

  * [SELECT – Cláusula ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre gatilhos, ações e limites desse conector, consulte os [detalhes de referência do conector](/connectors/sql/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

