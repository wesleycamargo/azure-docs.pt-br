<properties linkid="develop-node-how-to-sql-database" urlDisplayName="Banco de dados SQL" pageTitle="Guia de recursos Como usar o banco de dados SQL (Node.js) - Azure" metaKeywords="" description="Saiba como usar o banco de dados SQL do Azure do Node.js." metaCanonical="" services="sql-database" documentationCenter="Node.js" title="Como acessar o banco de dados SQL do Azure do Node.js" authors=""  solutions="" writer="" manager="" editor=""  />





#Como acessar o banco de dados SQL do Azure do Node.js

Este guia mostrará as noções básicas de como usar o Driver da Microsoft para o Node.JS, para que o SQL Server possa acessar um banco de dados SQL do Azure. Os cenários abordados incluem **criar um banco de dados SQL** e **conectar-se a um banco de dados SQL**. Este guia aborda a criação de um banco de dados SQL do [Portal de Gerenciamento de Visualização][preview-portal].

##Sumário

* [Conceitos](#Concepts)
* [Como: configurar seu ambiente](#Setup)
* [Como: criar um banco de dados SQL](#CreateServer)
* [Como: obter informações de conexão do banco de dados SQL](#ConnectionInfo)
* [Como: conectar-se a uma instância do Banco de Dados SQL](#Connect)
* [Considerações de implantação do Azure](#Deploy)
* [Próximas etapas](#NextSteps)

<h2><a id="Concepts"></a>Conceitos</h2>

###O que é o banco de dados SQL do Azure

O banco de dados SQL do Azure fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com o banco de dados SQL, você poderá provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que ofereça disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.

##O que é o Driver da Microsoft para o Node.JS para SQL Server

O Driver da Microsoft para o Node.JS para SQL Server permite que os desenvolvedores acessem os dados armazenados no Microsoft SQL Server ou no banco de dados SQL do Azure de um aplicativo Node.js. No momento, o driver é apenas uma versão de teste; os recursos adicionais serão integrados ao projeto quando estiverem concluídos. Para obter mais informações sobre o driver, consulte a [página do Github] do projeto Driver da Microsoft para o Node.JS para SQL Server e o [Wiki] associado.

<div class="dev-callout">
<b>Observação</b>
<p>O Driver da Microsoft para o Node.JS para SQL Server no momento está disponível como uma versão de teste e depende de componentes em tempo de execução que só estão disponíveis nos sistemas operacionais Microsoft Windows e Azure.</p>
</div>

<h2><a id="Setup"></a>Como: configurar seu ambiente</h2>

###Instalar o SQL Server Native Client

O Driver do Microsoft SQL Server para o Node.js depende do SQL Server Native Client. Embora o Native Client esteja automaticamente disponível quando o aplicativo é implantado no Azure, ele talvez não esteja presente no seu ambiente de desenvolvimento local. Você pode instalar o SQL Server Native Client a partir do página de download [Feature Pack do Microsoft SQL Server 2012].

<div class="dev-callout">
<b>Observação</b>
<p>O SQL Server Native Client só está disponível para o sistema operacional Microsoft Windows. Embora esse driver esteja disponível de forma nativa no Azure, você não conseguirá testar seu aplicativo localmente usando as informações deste artigo se estiver desenvolvendo em um sistema operacional diferente do Microsoft Windows.</p>
</div>

###Instalar o Node.js

O Node.js pode ser instalado de [http://nodejs.org/#download](http://nodejs.org/#download). Se um pacote de instalação não estiver disponível para o seu sistema operacional, você poderá compilar o Node.js da fonte.

<h2><a id="CreateServer"></a>Como: criar um banco de dados SQL</h2>

Siga estas etapas para criar um banco de dados SQL do Azure:

1. Faça logon no [Portal de Gerenciamento de Visualização][preview-portal].
2. Clique no ícone **+ Novo** na parte inferior esquerda do portal.

	![Criar um novo site do Azure][new-website]

3. Clique em **Banco de dados SQL** e, em seguida, em **Criação Personalizada**.

	![Criar um novo banco de dados SQL personalizado][custom-create]

4. Insira um valor para o **NAME** do seu banco de dados, selecione a **EDITION** (WEB ou BUSINESS), selecione o **MAX SIZE** para seu banco de dados, escolha o **COLLATION** e selecione **Novo Servidor do Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo. (Observe que, se tiver criado um banco de dados SQL anteriormente, você poderá escolher um servidor existente na lista suspensa **Escolher um servidor**.)

	![Preencher as configurações do banco de dados SQL][database-settings]

5. Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo banco de dados SQL será criado e marque a caixa `Permitir que serviços do Azure acessem o servidor`.

	![Criar um servidor de banco de dados SQL][create-server]

Para ver informações de servidor e banco de dados, clique em **Bancos de Dados SQL** no Portal de Gerenciamento de Visualização. Em seguida, você poderá clicar em **DATABASES** ou **SERVERS** para ver as informações relevantes.

![Exibir informações de servidor e banco de dados][sql-dbs-servers]

<h2><a id="ConnectionInfo"></a>Como: obter informações de conexão do banco de dados SQL</h2>

Para obter informações de conexão do banco de dados SQL, clique em **BANCOS DE DADOS SQL** no portal e, em seguida, clique no nome do banco de dados.

![Exibir informações de banco de dados][go-to-db-info]

Em seguida, clique em **Mostrar cadeias de conexão**.

![Mostrar Cadeias de Conexão][show-connection-string]

Na seção ODBC da janela resultante, anote os valores da cadeia de conexão. Esta é a cadeia de conexão que você usará ao se conectar ao banco de dados SQL do seu aplicativo de nó. A senha será a usada na criação do seu banco de dados SQL.

<h2><a id="Connect"></a>Como: conectar-se a uma instância do Banco de Dados SQL</h2>

###Instalar o node-sqlserver

O Driver da Microsoft para o Node.JS para SQL Server está disponível como o módulo nativo node-sqlserver. Uma versão binária desse módulo está disponível no [centro de download]. Para usar a versão binária, execute estas etapas:

1. Extraia o arquivo morto binário para o diretório **node\_modules** para o seu aplicativo.
2. Execute o arquivo **node-sqlserver-install.cmd** extraído do arquivo morto. Isso criará um subdiretório **node-sqlserver** em **node\_modules** e moverá os arquivos de driver para essa nova estrutura de diretórios.
3. Exclua o arquivo **node-sqlserver-install.cmd**, pois ele não é mais necessário.

  

<div class="dev-callout">
<b>Observação</b>
<p>Você também pode instalar o módulo node-sqlserver usando o utilitário npm; entretanto, isso invocará node-gyp para compilar uma versão binária do módulo no seu sistema.</p>
</div>

###Especificar a cadeia de conexão

Para usar o node-sqlserver, você deve solicitá-lo no seu aplicativo e especificar uma cadeia de conexão. A cadeia de conexão deve ser o valor ODBC retornado na seção [Como: obter informações de conexão do banco de dados SQL](#ConnectionInfo) deste artigo. O código deve ser semelhante ao seguinte:

    var sql = require('node-sqlserver');
	var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

###Consultar o banco de dados

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

<h2><a id="Deploy"></a>Considerações de implantação do Azure</h2>

<div class="dev-callout">
<b>Observação</b>
<p>As informações a seguir se baseiam em uma versão de teste do Driver da Microsoft para o Node.JS para SQL Server. As informações desta seção podem não ser as mais recentes sobre como usar o módulo node-sqlserver com o Azure. Você poderá obter as informações mais recentes na <a href="https://github.com/WindowsAzure/node-sqlserver">página do projeto Driver da Microsoft para o Node.JS para SQL Server</a> no Github.</p>
</div>

O Azure não instalará o módulo node-sqlserver dinamicamente em tempo de execução, portanto, você deve garantir que a implantação do seu aplicativo inclua uma versão binária do módulo. Você pode verificar se a sua implantação contém uma versão binária do módulo, garantindo a existência da estrutura de diretórios a seguir, e contém os arquivos descritos abaixo:

	application directory
		node_modules
			node-sqlserver
				lib

O diretório **node-sqlserver** deve conter um arquivo **package.json**. O diretório **lib** deve conter um **sql.js** e um arquivo **sqlserver.node**, que é a forma compilada do módulo node-sqlserver.

Para obter mais informações sobre como implantar um aplicativo Node.js no Azure, consulte [Criar e implantar um aplicativo Node.js em um site do Azure] e [Serviço de Nuvem do Node.js].

<h2><a id="NextSteps"></a>Próximas etapas</h2>

* [Introduzindo o Driver da Microsoft para o Node.JS para SQL Server]
* [Driver da Microsoft para o Node.js para SQL Server em Github.com]

[Serviço de Nuvem do Node.js]: /pt-br/develop/nodejs/tutorials/getting-started/
[Criar e implantar um aplicativo Node.js em um site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
[Introduzindo o Driver da Microsoft para o Node.JS para SQL Server]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
[Página do GitHub]: https://github.com/WindowsAzure/node-sqlserver
[Driver da Microsoft para o Node.js para SQL Server em Github.com]: https://github.com/WindowsAzure/node-sqlserver
[Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
[Instalando o Python e o SDK]: /pt-br/develop/python/common-tasks/install-python/
[Feature Pack do Microsoft SQL Server 2012]: http://www.microsoft.com/pt-br/download/details.aspx?id=29065
[preview-portal]: https://manage.windowsazure.com
[centro de download]: http://www.microsoft.com/pt-br/download/details.aspx?id=29995

[new-website]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
[custom-create]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
[database-settings]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
[create-server]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png



[go-to-db-info]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png

