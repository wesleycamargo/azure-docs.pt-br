<properties 
	pageTitle="Como gerenciar o Banco de dados SQL" 
	description="Saiba como gerenciar o banco de dados SQL do Azure." 
	headerExpose="" 
	footerExpose="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


## Visão geral

Este artigo mostra como executar tarefas de gerenciamento simples no Banco de Dados SQL do Azure. 

## Como: Conectar-se ao Banco de Dados SQL no Azure usando o Management Studio

O Management Studio é uma ferramenta administrativa que permite a você gerenciar várias instâncias do SQL Server e servidores em um único espaço de trabalho. Se já tiver uma instância do SQL Server local, você poderá abrir uma conexão com a instância local e um servidor lógico no Azure para executar as tarefas lado a lado.

O Management Studio inclui recursos que não estão disponíveis no momento no portal de gerenciamento, como um verificador de sintaxe e a capacidade de salvar scripts e consultas nomeadas para reutilização. O Banco de dados SQL é apenas um ponto de extremidade TDS. Qualquer ferramenta que funcione com o TDS, incluindo o Management Studio, é válida para as operações do Banco de Dados SQL. Os scripts que você desenvolver para o servidor local serão executado em um servidor lógico do Banco de Dados SQL. 

Na etapa seguinte, você usará o Management Studio para se conectar a um servidor lógico no Azure. Esta etapa requer que você tenha o SQL Server Management Studio versão 2008 R2 ou 2012. Se você precisar de ajuda para baixar ou conectar-se ao Management Studio, consulte Gerenciando o Banco de Dados SQL usando o Management Studio neste site. Esta etapa requer que você tenha o SQL Server Management Studio versão 2008 R2 ou 2012. Se você precisar de ajuda para baixar ou conectar-se ao Management Studio, consulte [Gerenciando o Banco de Dados SQL usando o Management Studio][] neste site.

Antes de você se conectar, às vezes, é necessário criar uma exceção de firewall que permita as solicitações de saída na porta 1433 no seu sistema local. Os computadores que, por padrão, estejam protegidos normalmente não têm a porta 1433 aberta. 

## Configurar o firewall para um servidor local

1. No Firewall do Windows com Segurança Avançada, crie uma nova regra de saída.

2. Escolha **Porta**, especificar TCP 1433, especifique **Permitir a conexão** e certifique-se de que o perfil **Público** esteja selecionado.

3. Atribua um nome significativo, como  *WindowsAzureSQLDatabase (tcp-out) port 1433*. 


## Conectar-se a um servidor lógico

1. No Management Studio, em Conectar-se ao Servidor, certifique-se de que o Mecanismo de Banco de Dados esteja selecionado e, em seguida, insira o nome do servidor lógico neste formato:  *servername*.database.widnows.net

	Você também pode obter o nome do servidor totalmente qualificado no portal de gerenciamento, no painel do servidor, em GERENCIAR URL.

2. Na Autenticação, escolha a **Autenticação do SQL Server** e insira o logon de administrador que você criou ao configurar o servidor lógico.

3. Clique em **Opções**. 

4. Em Conectar-se ao banco de dados, especifique **mestre**.


## Conectar-se a um servidor local

1. No Management Studio, em Conectar-se ao Servidor, certifique-se de que o Mecanismo de Banco de Dados esteja selecionado e, em seguida, insira o nome de uma instância local neste formato: *servername**instancename*. Se o servidor for local e uma instância padrão, digite *localhost*.

2. Na Autenticação, escolha **Autenticação do Windows** e insira uma conta do Windows que seja membro da função sysadmin.


## Como: Adicionar logons e usuários ao Banco de Dados SQL do Azure

Depois de implantar um banco de dados, você precisará configurar logons e atribuir permissões. Na próxima etapa, você executará dois scripts.

Para o primeiro script, você se conectará ao mestre e executará um script que cria os logons. Os logons serão usados para oferecer suporte às operações de leitura e gravação e para delegar as tarefas operacionais, como a capacidade de executar consultas do sistema sem as permissões 'sa'.

Os logons que você criar devem ser de autenticação do SQL Server. Se você já tiver scripts prontos que usam as identidades de usuário do Windows ou as identidades baseadas em declarações, esse script não funcionará no Banco de Dados SQL.

O segundo script atribui permissões de usuário do banco de dados. Para esse script, você se conectará a um banco de dados já carregado no Azure.

## Criar logons

1. No Management Studio, conecte-se a um servidor lógico no Azure, expanda a pasta Databases, clique com o botão direito do mouse em **mestre** e selecione **Nova Consulta**.

2. Use as seguintes instruções Transact-SQL para criar logons. Substitua a senha por uma senha válida. Selecione cada um individualmente e, em seguida, clique em **Executar**. Repita para os logons restantes.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## Criar usuários de banco de dados

1. Expanda a pasta Databases, clique com o botão do mouse em **school** e selecione **Nova Consulta**.

2. Use as seguintes instruções Transact-SQL para adicionar os usuários do banco de dados. Substitua a senha por uma senha válida. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Exibir e testar logons

1. Em uma nova janela de consulta, conecte-se a **mestre** e execute a seguinte instrução: 

        SELECT * from sys.sql_logins;

2. No Management Studio, clique com botão direito do mouse no banco de dados **school** e selecione **Nova Consulta**.

3. No menu Consulta, aponte para **Conexão**e, em seguida, clique em **Alterar Conexão**.

4. Faça o logon como *sqlreader*.

5. Copie e tente executar a instrução a seguir. Uma mensagem de erro deverá ser exibida informando que o objeto não existe.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Abra uma segunda janela de consulta e altere o contexto de conexão para  *sqlwriter*. A mesma consulta agora deve ser executada com êxito.

Agora você criou e testou vários logons. Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL][] e [Monitorando o Banco de Dados SQL usando as exibições de gerenciamento dinâmico][].

[Gerenciando bancos de dados e logons no banco de dados SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Gerenciamento de Banco de Dados SQL usando o Management Studio]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/






<!--HONumber=47-->
