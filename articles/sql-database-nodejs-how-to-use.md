<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Como acessar o banco de dados SQL do Azure do Node.js

Este guia mostrará as noções básicas de como usar o Driver da Microsoft para o Node.JS, para que o SQL Server possa acessar um banco de dados SQL do Azure. Os cenários abordados incluem **criar um banco de dados SQL** e **conectar-se a um banco de dados SQL**. Este guia aborda a criação de um Banco de Dados SQL a partir do [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

## Sumário

-   [Conceitos][Conceitos]
-   [Como: configurar seu ambiente][Como: configurar seu ambiente]
-   [Como: criar um banco de dados SQL][Como: criar um banco de dados SQL]
-   [Como: Obter informações da conexão do Banco de Dados SQL][Como: Obter informações da conexão do Banco de Dados SQL]
-   [Como: conectar-se à instância do banco de dados SQL][Como: conectar-se à instância do banco de dados SQL]
-   [Considerações de implantação do Azure][Considerações de implantação do Azure]
-   [Próximas etapas][Próximas etapas]

## <span id="Concepts"></span></a>Conceitos

### O que é Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com o banco de dados SQL, você poderá provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que ofereça disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.

## O que é o Driver da Microsoft para o Node.JS para SQL Server

O Driver da Microsoft para o Node.JS para SQL Server permite que os desenvolvedores acessem os dados armazenados no Microsoft SQL Server ou no banco de dados SQL do Azure de um aplicativo Node.js. No momento, o driver é apenas uma versão de teste; os recursos adicionais serão integrados ao projeto quando estiverem concluídos. Para obter mais informações sobre o driver, consulte a [página do Github][página do Github] do projeto Driver da Microsoft para o Node.JS para SQL Server e o [Wiki][Wiki] associado.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>O Driver da Microsoft para o Node.JS para SQL Server no momento est&aacute; dispon&iacute;vel como uma vers&atilde;o de teste e depende de componentes em tempo de execu&ccedil;&atilde;o que s&oacute; est&atilde;o dispon&iacute;veis nos sistemas operacionais Microsoft Windows e Azure.</p>
</div>

## <span id="Setup"></span></a>Como: configurar seu ambiente

### Instalar o SQL Server Native Client

O Driver do Microsoft SQL Server para o Node.js depende do SQL Server Native Client. Embora o Native Client esteja automaticamente disponível quando o aplicativo é implantado no Azure, ele talvez não esteja presente no seu ambiente de desenvolvimento local. Você pode instalar o SQL Server Native Client a partir do página de download [Feature Pack do Microsoft SQL Server 2012][Feature Pack do Microsoft SQL Server 2012].

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>O SQL Server Native Client s&oacute; est&aacute; dispon&iacute;vel para o sistema operacional Microsoft Windows. Embora esse driver esteja dispon&iacute;vel de forma nativa no Azure, voc&ecirc; n&atilde;o conseguir&aacute; testar seu aplicativo localmente usando as informa&ccedil;&otilde;es deste artigo se estiver desenvolvendo em um sistema operacional diferente do Microsoft Windows.</p>
</div>

### Instalar o Node.js

Node.js pode ser instalado em [][]<http://nodejs.org/#download></a>. Se um pacote de instalação não estiver disponível para o seu sistema operacional, você poderá compilar o Node.js da fonte.

## <span id="CreateServer"></span></a>Como: criar um banco de dados SQL

Siga estas etapas para criar um Banco de Dados SQL do Azure:

1.  Logon no [Portal de Gerenciamento][Portal de Gerenciamento do Azure].
2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **Banco de dados SQL** e, em seguida, em **Criação Personalizada**.

    ![Criar um novo banco de dados SQL personalizado][Criar um novo banco de dados SQL personalizado]

4.  Insira um valor para o **NOME** do seu banco de dados, selecione a **EDIÇÃO** (WEB ou BUSINESS), selecione o **TAMANHO MÁXIMO** de seu banco de dados, escolha o **AGRUPAMENTO** e selecione **NOVO Servidor do Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo. (Observe que, se tiver criado um banco de dados SQL anteriormente, você poderá escolher um servidor existente na lista suspensa **Escolher um servidor**.)

    ![Preencher as configurações do banco de dados SQL][Preencher as configurações do banco de dados SQL]

5.  Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo Banco de Dados SQL será criado e marque a caixa `Allow Azure Services to access the server`.

    ![Criar um novo servidor de banco de dados SQL][Criar um novo servidor de banco de dados SQL]

Para visualizar as informações do servidor e do banco de dados, clique em **Bancos de Dados SQL** no Portal de Gerenciamento do Azure. Em seguida, você poderá clicar em **BANCO DE DADOS** ou **SERVIDORES** para visualizar as informações relevantes.

![Exibir informações de servidor e banco de dados][Exibir informações de servidor e banco de dados]

## <span id="ConnectionInfo"></span></a>Como: Obter informações da conexão do Banco de Dados SQL

Para obter informações de conexão do banco de dados SQL, clique em **BANCOS DE DADOS SQL** no portal e, em seguida, clique no nome do banco de dados.

![Exibir informações de banco de dados][Exibir informações de banco de dados]

Em seguida, clique em **Mostrar cadeias de conexão**.

![Mostrar Cadeias de Conexão][Mostrar Cadeias de Conexão]

Na seção ODBC da janela resultante, anote os valores da cadeia de conexão. Esta é a cadeia de conexão que você usará ao se conectar ao banco de dados SQL do seu aplicativo de nó. A senha será a usada na criação do seu banco de dados SQL.

## <span id="Connect"></span></a>Como: conectar-se à instância do banco de dados SQL

### Instalar o node-sqlserver

O Driver da Microsoft para o Node.JS para SQL Server está disponível como o módulo nativo node-sqlserver. Uma versão binária desse módulo está disponível no [centro de download][centro de download]. Para usar a versão binária, execute estas etapas:

1.  Extraia o arquivo morto binário para o diretório **node\_modules** para o seu aplicativo.
2.  Execute o arquivo **node-sqlserver-install.cmd** extraído do arquivo morto. Isso criará um subdiretório **node-sqlserver** em **node\_modules** e moverá os arquivos de driver para essa nova estrutura de diretórios.
3.  Exclua o arquivo **node-sqlserver-install.cmd**, pois ele não é mais necessário.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Voc&ecirc; tamb&eacute;m pode instalar o m&oacute;dulo node-sqlserver usando o utilit&aacute;rio npm; entretanto, isso invocar&aacute; node-gyp para compilar uma vers&atilde;o bin&aacute;ria do m&oacute;dulo no seu sistema.</p>
</div>

### Especificar a cadeia de conexão

Para usar o node-sqlserver, você deve solicitá-lo no seu aplicativo e especificar uma cadeia de conexão. A cadeia de conexão deve ser o valor ODBC retornado na seção [Como: obter informações de conexão do banco de dados SQL][Como: Obter informações da conexão do Banco de Dados SQL] deste artigo. O código deve ser semelhante ao seguinte:

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Consultar o banco de dados

É possível realizar consultas, especificando uma instrução Transact-SQL com o método **query**. O código a seguir cria um servidor HTTP e retorna dados das linhas **ID**, **Column1** e **Column2** da tabela **Test** quando você exibe a página da web:

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

Embora o exemplo acima ilustre como retornar todas as linhas de uma só vez no conjunto de resultados, você também poderá retornar um objeto de instrução que lhe permita assinar eventos. Isso permitirá que você receba linhas e colunas individuais quando elas forem retornadas. O exemplo a seguir demonstra como fazer isso:

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

## <span id="Deploy"></span></a>Considerações de implantação do Azure

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>As informa&ccedil;&otilde;es a seguir se baseiam em uma vers&atilde;o de teste do Driver da Microsoft para o Node.JS para SQL Server. As informa&ccedil;&otilde;es desta se&ccedil;&atilde;o podem n&atilde;o ser as mais recentes sobre como usar o m&oacute;dulo node-sqlserver com o Azure. Voc&ecirc; poder&aacute; obter as informa&ccedil;&otilde;es mais recentes na <a href="https://github.com/WindowsAzure/node-sqlserver">p&aacute;gina do projeto Driver da Microsoft para o Node.JS para SQL Server</a> no Github.</p>
</div>

O Azure não instalará o módulo node-sqlserver dinamicamente em tempo de execução, portanto, você deve garantir que a implantação do seu aplicativo inclua uma versão binária do módulo. Você pode verificar se a sua implantação contém uma versão binária do módulo, garantindo a existência da estrutura de diretórios a seguir, e contém os arquivos descritos abaixo:

    application directory
        node_modules
            node-sqlserver
                lib

O diretório **node-sqlserver** deve conter um arquivo **package.json**. O diretório **lib** deve conter um **sql.js** e um arquivo **sqlserver.node**, que é a forma compilada do módulo node-sqlserver.

Para obter mais informações sobre como implantar um aplicativo Node.js no Azure, consulte [Criar e implantar um aplicativo Node.js em um site do Azure][Criar e implantar um aplicativo Node.js em um site do Azure] e [Serviço de Nuvem do Node.js][Serviço de Nuvem do Node.js].

## <span id="NextSteps"></span></a>Próximas etapas

-   [Introduzindo o Driver da Microsoft para o Node.JS para SQL Server][Introduzindo o Driver da Microsoft para o Node.JS para SQL Server]
-   [Driver da Microsoft para o Node.js para SQL Server em Github.com][página do Github]

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Conceitos]: #Concepts
  [Como: configurar seu ambiente]: #Setup
  [Como: criar um banco de dados SQL]: #CreateServer
  [Como: Obter informações da conexão do Banco de Dados SQL]: #ConnectionInfo
  [Como: conectar-se à instância do banco de dados SQL]: #Connect
  [Considerações de implantação do Azure]: #Deploy
  [Próximas etapas]: #NextSteps
  [página do Github]: https://github.com/WindowsAzure/node-sqlserver
  [Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Feature Pack do Microsoft SQL Server 2012]: http://www.microsoft.com/pt-br/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [Criar um novo site do Azure]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [Criar um novo banco de dados SQL personalizado]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [Preencher as configurações do banco de dados SQL]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [Criar um novo servidor de banco de dados SQL]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [Exibir informações de servidor e banco de dados]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [Exibir informações de banco de dados]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Mostrar Cadeias de Conexão]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [centro de download]: http://www.microsoft.com/pt-br/download/details.aspx?id=29995
  [Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
  [Serviço de Nuvem do Node.js]: /pt-br/develop/nodejs/tutorials/getting-started/
  [Introduzindo o Driver da Microsoft para o Node.JS para SQL Server]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
