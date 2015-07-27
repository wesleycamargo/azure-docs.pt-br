<properties 
	pageTitle="Como usar o banco de dados de SQL (PHP) - guias de recursos do Azure" 
	description="Aprenda a criar e conectar a um banco de dados SQL do Azure do PHP." 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#Como acessar o Banco de Dados SQL do Azure a partir do PHP 

## Visão geral

Este guia mostrará as noções básicas do uso do Banco de Dados SQL no PHP. As amostras são escritas em PHP. Os cenários abordados incluem **criar um banco de dados SQL** e **conectar-se a um banco de dados SQL**. Este guia aborda a criação de um Banco de Dados SQL a partir do [Portal de Gerenciamento][management-portal]. Para obter informações sobre como realizar essas tarefas a partir do portal de produção, consulte [Introdução ao PHP e ao Banco de Dados SQL][prod-portal-instructions] Para obter mais informações, consulte a seção [Próximas etapas](#NextSteps).

##O que é o Banco de Dados SQL?

O Banco de Dados SQL fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com o banco de dados SQL, você poderá provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que ofereça disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.

##<a id="Concepts"></a>Conceitos
Como o Banco de Dados SQL baseia-se em tecnologias do SQL Server, o acesso ao Banco de Dados SQL do PHP é muito semelhante ao acesso ao SQL Server do PHP. Você pode desenvolver um aplicativo localmente (usando o SQL Server) e depois conectar-se ao Banco de Dados SQL, alterando a cadeia de conexão. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)][limitations]

A abordagem recomendável para acessar o Banco de Dados SQL do PHP é usar os [Drivers da Microsoft para PHP para o SQL Server][download-drivers] (Os exemplos neste artigo usarão esses drivers). Os Drivers da Microsoft para PHP para o SQL Server só funcionam no Windows.

##<a id="Setup"></a>Como: configurar seu ambiente

A maneira recomendável para configurar o ambiente de desenvolvimento é usar o [Web Platform Installer da Microsoft][wpi-installer] O Web Platform Installer permitirá que você escolha os elementos de sua plataforma de desenvolvimento da web e automaticamente instale-os e configure-os. Ao baixar o Web Platform Installer e optar por instalar o WebMatrix, o PHP para WebMatrix e o SQL Server Express, um ambiente completo de desenvolvimento será definido para você.

Como alternativa, você pode configurar seu ambiente manualmente:

* Instale o PHP e configurar o IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Baixe e instale o SQL Server Express: [http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* Baixe e instale os [Drivers da Microsoft para PHP para o SQL Server][download-drivers].

##<a id="CreateServer"></a>Como: criar um banco de dados SQL

Siga estas etapas para criar um Banco de Dados SQL do Azure:

1. Logon no [Portal de Gerenciamento][management-portal].
2. Clique no ícone **Novo** localizado na parte inferior esquerda do portal.

	![Criar um novo site do Azure][new-website]

3. Clique em **SERVIÇOS DE DADOS**, **BANCO DE DADOS SQL** e depois em **CRIAÇÃO RÁPIDA**. Forneça um nome para o banco de dados, se deseja usar um servidor de banco de dados existente ou um novo, uma região e um nome de administrador e senha.

	![Criar um novo banco de dados SQL personalizado][quick-create]


Para visualizar as informações do servidor e do banco de dados, clique em **Bancos de Dados SQL** no Portal de Gerenciamento. Em seguida, você poderá clicar em **BANCO DE DADOS** ou **SERVIDORES** para visualizar as informações relevantes.

![Exibir informações de servidor e banco de dados][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Como: obter informações de conexão do banco de dados SQL

Para obter informações de conexão do banco de dados SQL, clique em **BANCOS DE DADOS SQL** no portal e, em seguida, clique no nome do banco de dados.

![Exibir informações de banco de dados][go-to-db-info]

Em seguida, selecione **Visualizar cadeias de conexão do Banco de Dados SQL para ADO.NET, ODBC, PHP e JDBC**.

![Mostrar Cadeias de Conexão][show-connection-string]

Na seção PHP da janela resultante, anote os valores de **SERVIDOR**, **BANCO DE DADOS** e **NOME DE USUÁRIO**. A senha será a usada na criação do seu banco de dados SQL.

##<a id="Connect"></a>Como: conectar-se a uma instância do Banco de Dados SQL

Os exemplos a seguir mostram como usar as extensões **SQLSRV** e **PDO_SQLSRV** para se conectar a um Banco de Dados SQL denominado`testdb`. Para obter informações sobre as APIs do **SQLSRV** e **PDO_SQLSRV**, consulte a [documentação de Drivers da Microsoft para PHP para o SQL Server][driver-docs]. Você precisará de informações obtidas na seção acima. Substitua `SERVER_ID` pela ID de 10 dígitos do servidor (que são os primeiros 10 caracteres do valor do SERVIDOR obtidos na seção acima) e atribua os valores corretos (seu nome de usuário e senha) às variáveis `$user` e `$pwd`.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>Próximas etapas
Como mencionado anteriormente, usar o Banco de Dados SQL é muito semelhante ao uso do SQL Server. Depois de ter estabelecido uma conexão com um Banco de Dados SQL (conforme mostrado acima), é possível usar as APIs do **SQLSRV** ou **PDO_SQLSRV** para inserir, recuperar, atualizar e excluir dados. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)][limitations]

Um exemplo que mostra como usar o banco de dados SQL no Azure está disponível em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=July15_HO3-->