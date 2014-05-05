<properties linkid="develop-php-sql-database" urlDisplayName="Banco de dados SQL" pageTitle="Como usar o Banco de Dados SQL (PHP) – guias di recurso do Azure" metaKeywords="PHP do Banco de Dados SQL do Azure, PHP do Banco de Dados SQL" description="Saiba como criar e se conectar a um Banco de Dados SQL do Azure a partir do PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="Como acessar o Banco de Dados SQL do Azure a partir do PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

#Como acessar o Banco de Dados SQL do Azure a partir do PHP 

Este guia mostrará as noções básicas do uso do Banco de Dados SQL do Azure a partir do PHP. As amostras são gravadas em PHP. Os cenários abordados incluem **criar um banco de dados SQL** e **conectar-se a um banco de dados SQL**. Este guia aborda a criação de um Banco de Dados SQL a partir do [Portal de Gerenciamento][preview-portal]. Para obter informações sobre como realizar essas tarefas a partir do portal de produção, consulte [Introdução ao PHP e ao Banco de Dados SQL][prod-portal-instructions] Para obter mais informações, consulte a seção [Próximas etapas](#NextSteps).

##O que é o Banco de Dados SQL do Azure?

O Banco de Dados SQL do Azure fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com o banco de dados SQL, você poderá provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que ofereça disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.

##Sumário

* [Conceitos](#Concepts)
* [Como configurar seu ambiente](#Setup)
* [Como criar um banco de dados SQL](#CreateServer)
* [Como obter informações de conexão do banco de dados SQL](#ConnectionInfo)
* [Como conectar-se a uma instância do Banco de Dados SQL](#Connect)
* [Próximas etapas](#NextSteps)

##<a id="Concepts"></a>Conceitos
Como o Banco de Dados SQL do Azure baseia-se em tecnologias do SQL Server, o acesso ao Banco de Dados SQL do PHP é muito semelhante ao acesso ao SQL Server do PHP. Você pode desenvolver um aplicativo localmente (usando o SQL Server) e depois conectar-se ao Banco de Dados SQL, alterando a cadeia de conexão. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e Limitações (Banco de Dados SQL)][limitations]

A abordagem recomendável para acessar o Banco de Dados SQL do PHP é usar os [Drivers da Microsoft para PHP para o SQL Server][download-drivers] (Os exemplos neste artigo usarão esses drivers). Os Drivers da Microsoft para PHP para o SQL Server só funcionam no Windows.

##<a id="Setup"></a>Como configurar seu ambiente

A maneira recomendável para configurar o ambiente de desenvolvimento é usar o [Web Platform Installer da Microsoft][wpi-installer] O Web Platform Installer permitirá que você escolha os elementos de sua plataforma de desenvolvimento da web e automaticamente instale-os e configure-os. Ao baixar o Web Platform Installer e optar por instalar o WebMatrix, o PHP para WebMatrix e o SQL Server Express, um ambiente completo de desenvolvimento será definido para você.

Como alternativa, você pode configurar seu ambiente manualmente:

* Instale o PHP e configure o IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Baixe e instale o SQL Server Express: [http://www.microsoft.com/pt-br/download/details.aspx?id=29062][install-sql-express]
* Baixe e instale os Drivers da Microsoft para PHP para o SQL Server: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a>Como criar um Banco de Dados SQL

Siga estas etapas para criar um Banco de Dados SQL do Azure:

1. Faça logon [Portal de Gerenciamento][preview-portal].
2. Clique no ícone **Novo** localizado na parte inferior esquerda do portal.

	![Criar um novo site do Azure][new-website]

3. Clique em **SERVIÇOS DE DADOS**, **BANCO DE DADOS SQL** e depois em **CRIAÇÃO PERSONALIZADA**.

	![Criar um novo banco de dados SQL personalizado][custom-create]

4. Insira um valor para o **NOME** do seu banco de dados, selecione a **EDIÇÃO** (WEB ou BUSINESS), selecione o **TAMANHO MÁXIMO** de seu banco de dados, escolha o **AGRUPAMENTO** e selecione **NOVO Servidor do Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo. (Observe que, se tiver criado um banco de dados SQL anteriormente, você poderá escolher um servidor existente na lista suspensa **Escolher um servidor**.)

	![Preencher as configurações do banco de dados SQL][database-settings]

5. Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo Banco de Dados SQL será criado e marque a caixa `Permitir que os serviços do Azure acessem o servidor`.

	![Criar um novo servidor de banco de dados SQL][create-server]

Para visualizar as informações do servidor e do banco de dados, clique em **Bancos de Dados SQL** no Portal de Gerenciamento. Em seguida, você poderá clicar em **BANCO DE DADOS** ou **SERVIDORES** para visualizar as informações relevantes.

![Exibir informações de servidor e banco de dados][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Como obter informações de conexão do Banco de Dados SQL

Para obter informações de conexão do banco de dados SQL, clique em **BANCOS DE DADOS SQL** no portal e, em seguida, clique no nome do banco de dados.

![Exibir informações de banco de dados][go-to-db-info]

Em seguida, selecione **Visualizar cadeias de conexão do Banco de Dados SQL para ADO.NET, ODBC, PHP e JDBC**.

![Mostrar Cadeias de Conexão][show-connection-string]

Na seção PHP da janela resultante, anote os valores de **SERVIDOR**, **BANCO DE DADOS** e **NOME DE USUÁRIO**. A senha será a usada na criação do seu banco de dados SQL.

##<a id="Connect"></a>Como conectar-se a uma instância do Banco de Dados SQL

Os exemplos a seguir mostram como usar as extensões **SQLSRV** e **PDO_SQLSRV** para se conectar a um Banco de Dados SQL denominado `testdb`. Você precisará de informações obtidas na seção acima. Substitua `SERVER_ID` pela ID de 10 dígitos do servidor (que são os primeiros 10 caracteres do valor do SERVIDOR obtidos na seção acima) e atribua os valores corretos (seu nome de usuário e senha) às variáveis `$user` e `$pwd`.

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
Como mencionado anteriormente, usar o Banco de Dados SQL é muito semelhante ao uso do SQL Server. Depois de ter estabelecido uma conexão com um Banco de Dados SQL (conforme mostrado acima), é possível usar as APIs do **SQLSRV** ou **PDO\_SQLSRV** para inserir, recuperar, atualizar e excluir dados. Para obter informações sobre as APIs do **SQLSRV** e **PDO\_SQLSRV**, consulte a [documentação de Drivers da Microsoft para PHP para o SQL Server][driver-docs] No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e Limitações (Banco de Dados SQL)][limitations]

Uma amostra que indica como usar o Banco de Dados SQL com PHP no Azure está disponível em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/pt-br/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
[preview-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png


