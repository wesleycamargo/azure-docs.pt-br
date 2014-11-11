<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Como acessar o Banco de Dados SQL do Azure a partir do PHP

Este guia mostrará as noções básicas do uso do Banco de Dados SQL do Azure a partir do PHP. As amostras são gravadas em PHP. Os cenários abordados incluem **criar um banco de dados SQL** e **conectar-se a um banco de dados SQL**. Este guia aborda a criação de um Banco de Dados SQL a partir do [Portal de Gerenciamento][Portal de Gerenciamento]. Para obter informações sobre como realizar essas tarefas a partir do portal de produção, consulte [Introdução ao PHP e ao Banco de Dados SQL][Introdução ao PHP e ao Banco de Dados SQL] Para obter mais informações, consulte a seção [Próximas etapas][Próximas etapas].

## O que é Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com o banco de dados SQL, você poderá provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que ofereça disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.

## Sumário

-   [Conceitos][Conceitos]
-   [Como: configurar seu ambiente][Como: configurar seu ambiente]
-   [Como: criar um banco de dados SQL][Como: criar um banco de dados SQL]
-   [Como: Obter informações da conexão do Banco de Dados SQL][Como: Obter informações da conexão do Banco de Dados SQL]
-   [Como: conectar-se à instância do banco de dados SQL][Como: conectar-se à instância do banco de dados SQL]
-   [Próximas etapas][Próximas etapas]

## <span id="Concepts"></span></a>Conceitos

Como o Banco de Dados SQL do Azure baseia-se em tecnologias do SQL Server, o acesso ao Banco de Dados SQL do PHP é muito semelhante ao acesso ao SQL Server do PHP. Você pode desenvolver um aplicativo localmente (usando o SQL Server) e depois conectar-se ao Banco de Dados SQL, alterando a cadeia de conexão. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)][Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)]

A abordagem recomendável para acessar o Banco de Dados SQL do PHP é usar os [Drivers da Microsoft para PHP para o SQL Server][Drivers da Microsoft para PHP para o SQL Server] (Os exemplos neste artigo usarão esses drivers). Os Drivers da Microsoft para PHP para o SQL Server só funcionam no Windows.

## <span id="Setup"></span></a>Como: configurar seu ambiente

A maneira recomendável para configurar o ambiente de desenvolvimento é usar o [Web Platform Installer da Microsoft][Web Platform Installer da Microsoft] O Web Platform Installer permitirá que você escolha os elementos de sua plataforma de desenvolvimento da web e automaticamente instale-os e configure-os. Ao baixar o Web Platform Installer e optar por instalar o WebMatrix, o PHP para WebMatrix e o SQL Server Express, um ambiente completo de desenvolvimento será definido para você.

Como alternativa, você pode configurar seu ambiente manualmente:

-   Instale o PHP e configure o IIS: [][]<http://php.net/manual/en/install.windows.iis7.php></a>.
-   Baixe e instale o SQL Server Express: [][1][http://www.microsoft.com/pt-br/download/details.aspx?id=29062][1]</a>
-   Baixe e instale os Drivers da Microsoft para PHP para o SQL Server: [][2]<http://php.net/manual/en/sqlsrv.requirements.php></a>.

## <span id="CreateServer"></span></a>Como: criar um banco de dados SQL

Siga estas etapas para criar um Banco de Dados SQL do Azure:

1.  Logon no [Portal de Gerenciamento][Portal de Gerenciamento].
2.  Clique no ícone **Novo** localizado na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **SERVIÇOS DE DADOS**, **BANCO DE DADOS SQL** e depois em **CRIAÇÃO PERSONALIZADA**.

    ![Criar um novo banco de dados SQL personalizado][Criar um novo banco de dados SQL personalizado]

4.  Insira um valor para o **NOME** do seu banco de dados, selecione a **EDIÇÃO** (WEB ou BUSINESS), selecione o **TAMANHO MÁXIMO** de seu banco de dados, escolha o **AGRUPAMENTO** e selecione **NOVO Servidor do Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo. (Observe que, se tiver criado um banco de dados SQL anteriormente, você poderá escolher um servidor existente na lista suspensa **Escolher um servidor**.)

    ![Preencher as configurações do banco de dados SQL][Preencher as configurações do banco de dados SQL]

5.  Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo Banco de Dados SQL será criado e marque a caixa `Allow Azure Services to access the server`.

    ![Criar um novo servidor de banco de dados SQL][Criar um novo servidor de banco de dados SQL]

Para visualizar as informações do servidor e do banco de dados, clique em **Bancos de Dados SQL** no Portal de Gerenciamento. Em seguida, você poderá clicar em **BANCO DE DADOS** ou **SERVIDORES** para visualizar as informações relevantes.

![Exibir informações de servidor e banco de dados][Exibir informações de servidor e banco de dados]

## <span id="ConnectionInfo"></span></a>Como: Obter informações da conexão do Banco de Dados SQL

Para obter informações de conexão do banco de dados SQL, clique em **BANCOS DE DADOS SQL** no portal e, em seguida, clique no nome do banco de dados.

![Exibir informações de banco de dados][Exibir informações de banco de dados]

Em seguida, selecione **Visualizar cadeias de conexão do Banco de Dados SQL para ADO.NET, ODBC, PHP e JDBC**.

![Mostrar Cadeias de Conexão][Mostrar Cadeias de Conexão]

Na seção PHP da janela resultante, anote os valores de **SERVIDOR**, **BANCO DE DADOS** e **NOME DE USUÁRIO**. A senha será a usada na criação do seu banco de dados SQL.

## <span id="Connect"></span></a>Como: conectar-se à instância do banco de dados SQL

Os exemplos a seguir mostram como usar as extensões **SQLSRV** e **PDO\_SQLSRV** para se conectar a um Banco de Dados SQL denominado `testdb`. Você precisará de informações obtidas na seção acima. Substitua `SERVER_ID` pela ID de 10 dígitos do servidor (que são os primeiros 10 caracteres do valor do SERVIDOR obtidos na seção acima) e atribua os valores corretos (seu nome de usuário e senha) às variáveis `$user` e `$pwd`.

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

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

## <span id="NextSteps"></span></a>Próximas etapas

Como mencionado anteriormente, usar o Banco de Dados SQL é muito semelhante ao uso do SQL Server. Depois de ter estabelecido uma conexão com um Banco de Dados SQL (conforme mostrado acima), é possível usar as APIs do **SQLSRV** ou **PDO\_SQLSRV** para inserir, recuperar, atualizar e excluir dados. Para obter informações sobre as APIs do **SQLSRV** e **PDO\_SQLSRV**, consulte a [documentação de Drivers da Microsoft para PHP para o SQL Server][documentação de Drivers da Microsoft para PHP para o SQL Server]. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)][Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)]

Um exemplo que mostra como usar o banco de dados SQL no Azure está disponível em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Introdução ao PHP e ao Banco de Dados SQL]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
  [Próximas etapas]: #NextSteps
  [Conceitos]: #Concepts
  [Como: configurar seu ambiente]: #Setup
  [Como: criar um banco de dados SQL]: #CreateServer
  [Como: Obter informações da conexão do Banco de Dados SQL]: #ConnectionInfo
  [Como: conectar-se à instância do banco de dados SQL]: #Connect
  [Drivers da Microsoft para PHP para o SQL Server]: http://www.microsoft.com/download/en/details.aspx?id=20098
  [Web Platform Installer da Microsoft]: http://go.microsoft.com/fwlink/?LinkId=253447
  []: http://php.net/manual/en/install.windows.iis7.php
  [1]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
  [2]: http://php.net/manual/en/sqlsrv.requirements.php
  [Criar um novo site do Azure]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
  [Criar um novo banco de dados SQL personalizado]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
  [Preencher as configurações do banco de dados SQL]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
  [Criar um novo servidor de banco de dados SQL]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
  [Exibir informações de servidor e banco de dados]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
  [Exibir informações de banco de dados]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
  [Mostrar Cadeias de Conexão]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
  [documentação de Drivers da Microsoft para PHP para o SQL Server]: http://msdn.microsoft.com/pt-br/library/dd638075(SQL.10).aspx
