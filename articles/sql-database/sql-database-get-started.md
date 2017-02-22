---
title: "Início rápido: seu primeiro banco de dados SQL | Microsoft Docs"
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall de nível de servidor e bancos de dados com o portal do Azure. Você também aprenderá a usar o SQL Server Management Studio com o banco de dados Azure SQL."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6453cca9f876e6c363fbed463263c0f9684a3e70
ms.openlocfilehash: b838974de06ecbc751254064e2310df51c450086


---
# <a name="quick-start-tutorial-your-first-azure-sql-database"></a>Tutorial de início rápido: seu primeiro banco de dados do Azure SQL

Neste tutorial de início rápido, você aprenderá como:

* [Configurar um novo servidor lógico](sql-database-get-started.md#create-a-new-logical-sql-server) 
* [Exibir as propriedades de servidor lógico](sql-database-get-started.md#view-the-logical-server-properties) 
* [Criar uma regra de firewall no nível de servidor](sql-database-get-started.md#create-a-server-level-firewall-rule) 
* [Conectar ao servidor com SSMS](sql-database-get-started.md#connect-to-the-server-with-ssms) 
* [Criar um banco de dados com dados de exemplo](sql-database-get-started.md#create-a-database-with-sample-data) 
* [Exibir as propriedades de banco de dados](sql-database-get-started.md#view-the-database-properties) 
* [Consultar o banco de dados no portal do Azure](sql-database-get-started.md#query-the-database-in-the-azure-portal) 
* [Conectar-se e consultar o banco de dados com SSMS](sql-database-get-started.md#connect-and-query-the-database-with-ssms) 
* [Criar um banco de dados em branco com SSMS](sql-database-get-started.md#create-a-blank-database-with-ssms) 
* [Solucionar problemas de conectividade](sql-database-get-started.md#troubleshoot-connectivity) 
* [Excluir um banco de dados](sql-database-get-started.md#delete-a-single-database) 


Neste tutorial de início rápido, você cria um banco de dados de exemplo e um banco de dados em branco em execução em um grupo de recursos do Azure e conectado a um servidor lógico. Você também cria duas regras de firewall de nível de servidor configuradas para habilitar a entidade de nível de servidor a fazer logon no servidor por meio de dois endereços IP especificados. Por fim, você saiba como consultar um banco de dados no portal do Azure e conectar e consultar usando SQL Server Management Studio. 

**Tempo estimado**: este tutorial leva cerca de 30 minutos (supondo que você já atenda aos pré-requisitos).

> [!TIP]
> Você pode executar as mesmas tarefas com [C#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Você deve ser capaz de se conectar ao portal do Azure com uma conta que seja membro da função de colaborador ou proprietário da assinatura. Para obter mais informações sobre o controle de acesso baseado em função (RBAC), confira [Introdução ao gerenciamento de acesso no Portal do Azure](../active-directory/role-based-access-control-what-is.md).

> [!NOTE]
> Este tutorial de início rápido o ajuda a obter o conteúdo destes tópicos de aprendizagem: [Visão geral do servidor de Banco de Dados SQL](sql-database-server-overview.md), [Visão geral do banco de dados SQL](sql-database-overview.md) e [Visão geral de regras de firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md).
>  


### <a name="sign-in-to-the-azure-portal-with-your-azure-account"></a>Entre no portal do Azure com sua conta do Azure
com a [conta do Azure](https://account.windowsazure.com/Home/Index), siga estas etapas para se conectar ao portal do Azure.

1. Abra seu navegador preferido e conecte-se ao [portal do Azure](https://portal.azure.com/).
2. Entre no [Portal do Azure](https://portal.azure.com/).
3. Na página **Entrar** , forneça as credenciais da sua assinatura.
   
   ![Entrar](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Criar um novo servidor SQL lógico

Siga as etapas neste procedimento para criar um novo servidor lógico com o portal do Azure na região de sua escolha.

1. Clique em **Novo**, digite **sql server** e, em seguida, clique em **ENTER**.

    ![servidor SQL lógico](./media/sql-database-get-started/logical-sql-server.png)
2. Clique em **SQL server (servidor lógico)**.
   
    ![criar lógica do sql server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Clique em **Criar** para abrir a nova folha do SQL Server (servidor lógico).

    ![nova lógica do sql server](./media/sql-database-get-started/new-logical-sql-server.png)
3. Na caixa de texto Nome do servidor, forneça um nome válido para o novo servidor lógico. Uma marca de seleção verde indica que você forneceu um nome válido.
    
    ![novo nome do servidor](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > O nome totalmente qualificado para o novo servidor terá o formato: <nome_do_servidor>.database.windows.net.
    >
    
4. Na caixa de texto de logon do administrador de servidor, forneça um nome de usuário para o logon de autenticação do SQL para este servidor. Esse logon é conhecido como o logon principal do servidor. Uma marca de seleção verde indica que você forneceu um nome válido.
    
    ![Logon de administrador do SQL](./media/sql-database-get-started/sql-admin-login.png)
5. Nas caixas de texto **Senha** e **Confirmar senha**, forneça uma senha para a conta de logon da entidade de segurança. Uma marca de seleção verde indica que você forneceu uma senha válida.
    
    ![Senha de administrador do SQL](./media/sql-database-get-started/sql-admin-password.png)
6. Selecione uma assinatura em que você tenha permissão para criar objetos.

    ![subscription](./media/sql-database-get-started/subscription.png)
7. Na caixa de texto Grupo de recursos, selecione **Criar novo** e, na caixa de texto grupo de recursos, forneça um nome válido para o novo grupo de recursos (você também pode usar um grupo de recursos existente, caso já tenha criado um). Uma marca de seleção verde indica que você forneceu um nome válido.

    ![novo grupo de recursos](./media/sql-database-get-started/new-resource-group.png)

8. Na caixa de texto **Local**, selecione um data center apropriado para seu local, como "Leste da Austrália".
    
    ![local do servidor](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > A caixa de seleção para **Permitir que os serviços do Azure acessem o servidor** não pode ser alterado nessa folha. Você pode alterar essa configuração na folha de firewall do servidor. Para obter mais informações, confira [Introdução à segurança](sql-database-control-access-sql-authentication-get-started.md).
    >
    
9. Clique em **Criar**.

    ![botão criar](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-server-properties"></a>Exibir as propriedades de servidor lógico

Siga as etapas neste procedimento para exibir as propriedades do servidor com o portal do Azure. Você precisa do nome totalmente qualificado do servidor para se conectar a esse servidor em um procedimento subsequente. 

1. No portal do Azure, clique em **Mais serviços**.

    ![mais serviços](./media/sql-database-get-started/more-services.png)
2. Na caixa de texto de Filtro, digite **SQL** e, em seguida, clique na estrela de servidores SQL para especificar servidores SQL como um favorito no Azure. 

    ![definir favoritos](./media/sql-database-get-started/favorite.png)
3. Na folha padrão, clique em **SQL servers** para abrir a lista de servidores SQL em sua assinatura do Azure. 

    ![novo sql server](./media/sql-database-get-started/new-sql-server.png)

4. Clique em seu novo SQL server para exibir suas propriedades no portal do Azure. Tutoriais subsequentes o ajudarão a entender as opções disponíveis nessa folha.

    ![folha do sql server](./media/sql-database-get-started/sql-server-blade.png)
5. Em Configurações, clique em **Propriedades** para exibir várias propriedades do SQL server lógico.

    ![propriedades do sql server](./media/sql-database-get-started/sql-server-properties.png)
6. Copie o nome totalmente qualificado do servidor para a área de transferência para usar posteriormente neste tutorial.

    ![nome completo do sql server](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

Siga as etapas neste procedimento para criar uma nova regra de firewall de nível de servidor com o portal do Azure para que você possa se conectar ao servidor com o SQL Server Management Studio no próximo procedimento.

1. Na folha do SQL server, em Configurações, clique em **Firewall** para abrir a folha de Firewall para o SQL server.

    ![firewall do sql server](./media/sql-database-get-started/sql-server-firewall.png)

2. Clique em **Adicionar IP do cliente** na barra de ferramentas.

    ![adicionar IP do cliente](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Você pode abrir o firewall do Banco de dados SQL no servidor para um único endereço IP ou um intervalo inteiro de endereços. Abrir o firewall permite que os administradores do SQL e os usuários façam logon em qualquer banco de dados no servidor ao qual eles têm credenciais válidas.
    >

4. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall de nível de servidor e, em seguida, clique em **OK**.

    ![adicionar IP do cliente](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Conectar ao servidor com SSMS

Siga as etapas neste procedimento para se conectar ao servidor lógico SQL com o SQL Server Management Studio.

1. Se você ainda não o fez, baixe e instale a versão mais recente do SSMS em [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para se manter atualizado, a versão mais recente do SSMS avisará você quando houver uma nova versão disponível para download.

2. Depois de instalar, digite **Microsoft SQL Server Management Studio** na caixa de pesquisa do Windows e clique em **Enter** para abrir o SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Na caixa de diálogo Conectar ao servidor, insira as informações necessárias para se conectar ao SQL server com a Autenticação do SQL Server.

    ![conectar-se ao servidor](./media/sql-database-get-started/connect-to-server.png)
4. Clique em **Conectar**.

    ![conectado ao servidor](./media/sql-database-get-started/connected-to-server.png)
5. No Pesquisador de objetos, expanda **Bancos de dados**, expanda **Bancos de dados do sistema** e expanda **mestre** para exibir objetos no banco de dados mestre.

    ![banco de dados mestre](./media/sql-database-get-started/master-database.png)
6. Clique com o botão direito do mouse em **mestre** e depois clique em **Nova consulta**.

    ![consultar o banco de dados mestre](./media/sql-database-get-started/query-master-database.png)

8. Na janela de consulta, digite o seguinte:

   ```select * from sys.objects```

9.  Na barra de ferramentas, clique em **Executar** para retornar uma lista de todos os objetos de sistema no banco de dados mestre.

    ![consultar objetos de sistema de banco de dados mestre](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Para explorar a segurança do SQL, confira [Introdução à segurança do SQL](sql-database-control-access-sql-authentication-get-started.md)
    >

## <a name="create-a-database-with-sample-data"></a>Criar um banco de dados com dados de exemplo

Siga as etapas neste procedimento para criar um banco de dados com dados de exemplo com o portal do Azure. Você cria esse banco de dados anexado ao servidor lógico que criou anteriormente. Se a camada de serviço Básica não estiver disponível na região em que você criou o servidor, exclua o servidor e recrie-o em outra região. Para obter etapas de exclusão, confira o último procedimento deste tutorial.

1. No Portal do Azure, clique em **Bancos de dados SQL** na folha padrão.

    ![bancos de dados sql](./media/sql-database-get-started/new-sql-database.png)
2. Na folha dos Bancos de dados SQL, clique em **Adicionar**.

    ![adicionar banco de dados sql](./media/sql-database-get-started/add-sql-database.png)
3. Na folha do Banco de dados SQL, examine as informações concluídas para você.

    ![folha do banco de dados sql](./media/sql-database-get-started/sql-database-blade.png)
4. Forneça um nome de banco de dados válido.

    ![nome do banco de dados sql](./media/sql-database-get-started/sql-database-name.png)
5. Em Selecionar fonte, clique em **Exemplo** e depois em Selecionar exemplo, clique em **AdventureWorksLT [V12]**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
6. Em servidor, forneça o nome de usuário de logon de administrador de servidor e a senha.

    ![credenciais do servidor](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Ao adicionar um banco de dados a um servidor, ele pode ser adicionado como um banco de dados individual (esse é o padrão) ou adicionado a um pool elástico. Para saber mais sobre pools elásticos, confira [Pools elásticos](sql-database-elastic-pool.md).
    >

7. No tipo de preço, altere o tipo de preço para **Básico** (você pode aumentar o tipo de preço mais tarde, se desejado, mas para fins de aprendizado, recomendamos que você use a camada de custo mais baixa).

    ![Tipo de preço](./media/sql-database-get-started/pricing-tier.png)
8. Clique em **Criar**.

    ![botão criar](./media/sql-database-get-started/create.png)

## <a name="view-the-database-properties"></a>Exibir as propriedades de banco de dados

Siga as etapas neste procedimento para consultar o banco de dados com o portal do Azure.

1. Na folha de bancos de dados SQL, clique em seu novo banco de dados para exibir suas propriedades no portal do Azure. Tutoriais subsequentes o ajudarão a entender as opções disponíveis nessa folha. 

    ![nova folha de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)
2. Clique em **Propriedades** para exibir informações adicionais sobre seu banco de dados.

    ![novas propriedades de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-properties.png)

3. Clique em **Mostrar cadeias de conexão de banco de dados**.

    ![novas cadeias de conexão de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Clique em **Visão geral** e, em seguida, clique no nome do servidor no painel do Essentials.
    
    ![novo painel do Essentials de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. No painel do Essentials para o servidor, confira seu banco de dados adicionado recentemente.

    ![novo banco de dados de exemplo no painel servidor do Essentials](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="query-the-database-in-the-azure-portal"></a>Abra o banco de dados no Portal do Azure

Siga as etapas neste procedimento para consultar o banco de dados com o editor de consultas no portal do Azure. A consulta mostra os objetos no banco de dados.

1. Na folha de bancos de dados SQL, clique em **Ferramentas** na barra de ferramentas.

    ![ferramentas](./media/sql-database-get-started/tools.png)
2. Na folha Ferramentas, clique em **Editor de consultas (visualização)**.

    ![editor de consultas](./media/sql-database-get-started/query-editor.png)
3. Clique na caixa de seleção para confirmar que o editor de consultas é um recurso de visualização e clique em **OK**.
4. Na folha **Editor de consultas**, clique em **Logon**.

    ![folha de editor de consultas](./media/sql-database-get-started/query-editor-blade.png)
5. Examine o tipo de autorização e o logon e forneça a senha para esse logon. 

    ![logon do editor de consultas](./media/sql-database-get-started/query-editor-login.png)
6. Clique em **OK** para tentar fazer logon.
7. Ao receber um erro de logon, informando que o cliente não tem permissão para fazer logon devido a ausência de uma regra de firewall para o endereço IP do cliente, copie o endereço IP do cliente na janela de erro e, na folha do servidor SQL do banco de dados, crie uma regra de firewall de nível de servidor.

    ![erro do editor de consultas](./media/sql-database-get-started/query-editor-error.png)
8. Repita as seis etapas anteriores para fazer logon no banco de dados.
9. Depois de autenticado, na janela de consulta, digite a seguinte consulta:

   ```select * from sys.objects```

    ![consulta do editor de consultas](./media/sql-database-get-started/query-editor-query.png)
10.  Clique em **Executar**.
11. Examine os resultados da consulta no painel **Resultados**.

    ![resultados do editor de consultas](./media/sql-database-get-started/query-editor-results.png)

## <a name="connect-and-query-the-database-with-ssms"></a>Conectar-se e consultar o banco de dados com SSMS

Siga as etapas neste procedimento para se conectar ao banco de dados com o SQL Server Management Studio e consultar os dados de exemplo para exibir os objetos no banco de dados.

1. Alterne para o SQL Server Management Studio e, no Pesquisador de Objetos, clique em **Bancos de dados** e, em seguida, clique em **Atualizar** na barra de ferramentas para exibir o banco de dados de exemplo.

    ![novo banco de dados de exemplo com o ssms](./media/sql-database-get-started/new-sample-db-ssms.png)
2. No Pesquisador de Objetos, expanda o novo banco de dados para exibir seus objetos.

    ![novos objetos de banco de dados de exemplo com o ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Clique em seu banco de dados de exemplo e depois clique em **Nova Consulta**.

    ![nova consulta de banco de dados de exemplo com o ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Na janela de consulta, digite o seguinte:

   ```select * from sys.objects```
   
9.  Na barra de ferramentas, clique em **Executar** para retornar uma lista de todos os objetos de sistema no banco de dados de exemplo.

    ![novos objetos de sistema de consulta de banco de dados de exemplo com o ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>Criar um banco de dados em branco com SSMS

Siga as etapas neste procedimento para criar um novo banco de dados no servidor lógico com o SQL Server Management Studio.

1. No Pesquisador de Objetos, clique com o botão direito do mouse em **Bancos de Dados** e em **Novo banco de dados**.

    ![novo banco de dados em branco com o ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > Você também pode fazer com que o SSMS crie um script de criação de banco de dados para criar um novo banco de dados com o Transact-SQL.
    >

2. Na caixa de diálogo Novo Banco de Dados, forneça um nome de banco de dados na caixa de texto de Nome do banco de dados. 

    ![novo nome de banco de dados em branco com o ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. Na caixa de diálogo Novo Banco de Dados, clique em **Opções** e, em seguida, altere a edição para **Básica**.

    ![novas opções de banco de dados em branco com o ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Analise as outras opções na caixa de diálogo que você pode modificar em um Banco de dados SQL do Azure. Para saber mais informações sobre estas opções, confira [Criar Banco de dados](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Clique em **OK** para criar o banco de dados em branco.
5. Ao concluir, atualize o nó do Banco de dados no Pesquisador de Objetos para exibir o banco de dados em branco recém-criado. 

    ![novo banco de dados em branco no pesquisador de objetos](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Solucionar problemas de conectividade

> [!IMPORTANT]
> Se tiver problemas de conectividade, confira [Problemas de conectividade](sql-database-troubleshoot-common-connection-issues.md).
> 

## <a name="delete-a-single-database"></a>Excluir um único banco de dados

Siga as etapas neste procedimento para excluir um banco de dados com o portal do Azure.

1. Na folha no portal do Azure para o banco de dados SQL, clique em **Excluir**.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Clique em **Sim** para confirmar que deseja excluir este banco de dados permanentemente.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Durante o período de retenção do banco de dados, você pode restaurar os backups automáticos iniciados pelo serviço. Para bancos de dados edição Básica, você poderá restaurá-los dentro de sete dias. No entanto, não exclua um servidor. Se você fizer isso, não poderá recuperar o servidor ou qualquer um de seus bancos de dados excluídos. Para obter mais informações sobre backups de banco de dados, confira [Saiba mais sobre backups de banco de dados SQL](sql-database-automated-backups.md) e para obter informações sobre como restaurar um banco de dados por meio de backups, confira [Recuperação de banco de dados](sql-database-recovery-using-backups.md). Para obter um artigo de instruções sobre como restaurar um banco de dados excluído, confira [Restaurar um banco de dados Azure SQL excluído - portal do Azure](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial, há vários tutoriais adicionais que talvez você queira explorar que criam o que você aprendeu neste tutorial. 

- Para obter uma introdução ao tutorial de autenticação do SQL Server, confira [Autenticação e autorização do SQL](sql-database-control-access-sql-authentication-get-started.md)
- Para obter uma introdução ao tutorial de autenticação do Azure Active Directory, confira [Autenticação e autorização do AAD](sql-database-control-access-aad-authentication-get-started.md)
* Se você deseja consultar o banco de dados de exemplo no portal do Azure, confira [Visualização pública: experiência interativa de consulta para bancos de dados SQL](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/)
* Se você conhece o Excel, saiba como [Conectar um banco de dados SQL no Azure com o Excel](sql-database-connect-excel.md).
* Se você estiver pronto para começar a codificar, escolha a linguagem de programação em [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md).
* Se você quiser mover seus bancos de dados SQL Server locais para o Azure, confira [Migrando um banco de dados para o Banco de Dados SQL](sql-database-cloud-migrate.md).
* Se quiser carregar alguns dados em uma nova tabela de um arquivo CSV com a ferramenta da linha de comando BCP, confira [Carregando dados no Banco de Dados SQL a partir de um arquivo CSV com o BCP](sql-database-load-from-csv-with-bcp.md).
* Se você deseja iniciar a criação de tabelas e outros objetos, confira o tópico "Para criar uma tabela" em [Criando uma tabela](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionais

- Para uma visão geral técnica, confira [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para saber mais sobre preços, confira [Preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO2-->


