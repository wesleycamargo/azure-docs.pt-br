<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Site com armazenamento" pageTitle="Site do Node.js com o armazenamento de tabelas | Microsoft Azure" metaKeywords="Node.js com armazenamento de tabelas no Azure, aplicativo Node.js no Azure, tutorial do Node.js no Azure, exemplo do Node.js no Azure" description="Um tutorial que ensina a usar o serviço de Tabela do Azure para armazenar os dados a partir de um aplicativo de Nó hospedado em um site do Azure." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Aplicativo da web do Node.js que usa o Serviço de Tabela do Azure" authors="" solutions="" manager="" editor="" />





# Aplicativo da web do Node.js que usa o Serviço de Tabela do Azure

Este tutorial mostra como usar o serviço de Tabela fornecido pelo Gerenciamento de Dados do Azure para armazenar e acessar dados em um aplicativo de [nó] hospedado no Azure. Este tutorial pressupõe que você tenha alguma experiência anterior usando nó e [Git].

Você irá aprender:

* Como usar npm (gerenciador de pacote de nós) para instalar os módulos do nó

* Como trabalhar com o serviço de Tabela do Azure

* Como usar a ferramenta de linha de comando do Azure para o Mac e o Linux para criar um site do Azure

Seguindo este tutorial, você criará um aplicativo simples de gerenciamento de tarefas baseado na web que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas no serviço de Tabela.
 
Os arquivos do projeto para este tutorial serão armazenados em um diretório chamado **tasklist** e o aplicativo concluído será semelhante ao seguinte:

![Uma página da Web que exibe uma lista de tarefas vazia][node-table-finished]

<div class="dev-callout">
<strong>Observação</strong>
<p>Este tutorial faz referência à pasta <strong>tasklist</strong>. O caminho completo da pasta é omitido, já que a semântica de caminho depende dos sistemas operacionais. Você deve criar essa pasta em um local de fácil acesso em seu sistema de arquivos local, como <strong>~/node/tasklist</strong> ou <strong>c:\node\tasklist</strong></p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>Muitas das etapas abaixo mencionam o uso da linha de comando. Para essas etapas, use a linha de comando de seu sistema operacional, como <strong>cmd.exe</strong> (Windows) ou <strong>Bash</strong> (Unix Shell). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo Terminal.</p>
</div>

##Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

* [node] versão 0.6.14 ou superior

* [Git]

* Um editor de texto

* Um navegador da web

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Criar uma conta de armazenamento

Você pode criar uma conta de armazenamento seguindo essas etapas. Essa conta será usada em instruções subsequentes neste tutorial.

1. Abra seu navegador e vá ate o [Portal do Azure]. Se solicitado, faça login com as informações de assinatura do Azure.

2. Clique em **+ NOVO** na parte inferior da página e selecione **Conta de Armazenamento**.

	![+novo][portal-new]

	![Conta de Armazenamento][portal-storage-account]

3. Selecione **Criação Rápida** e depois insira a URL e Região/Afinidade para esta conta de armazenamento. Já que esse é um tutorial e não precisa ser replicado globalmente, desmarque **Habilitar Replicação Geográfica**. Por fim, clique em "Criar Conta de Armazenamento".

	![Criação Rápida][portal-quick-create-storage]

	Anote a URL que você digitar, pois ela será referenciada como o nome da conta, em etapas posteriores.

4. Depois que a conta de armazenamento tiver sido criada, clique em **Gerenciar chaves** na parte inferior da página. Isso exibirá as teclas de acesso primário e secundário para esta conta de armazenamento. Copie e salve a chave de acesso primário e clique na marca de seleção.

	![teclas de acesso][portal-storage-access-keys]

##Instalar módulos e gerar scaffolding

Nesta seção você criará um novo aplicativo de nó e usará o npm para adicionar pacotes de módulo. Para o aplicativo da lista de tarefas, você usará os módulos [Express] e [Azure]. O módulo Express fornece uma estrutura de Controlador da Exibição do Modelo para o nó, enquanto que os módulos do Azure fornecem a conectividade ao serviço de Tabela.

###Instalar o express e gerar scaffolding

1. Na linha de comando, altere os diretórios para o diretório **tasklist**. Se o diretório **tasklist** não existir, crie-o.

2. Digite o seguinte comando para instalar o Express.

		npm install express -g

    <div class="dev-callout">
	<strong>Observação</strong>
	<p>Ao usar o parâmetro '-g' em alguns sistemas operacionais, você pode receber a mensagem <strong>erro: EPERM, chmod '/ usr/local/bin/express'</strong> e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando <strong>sudo</strong> para executar o npm em um nível de privilégio mais elevado.</p>
	</div>

    A saída deste comando deve ser semelhante ao seguinte:

		express@3.4.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.2.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, multiparty@2.1.8)

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>O parâmetro '-g' usado durante a instalação do módulo express o instala globalmente. Isso é feito para que possamos acessar o comando <strong>express</strong> para gerar o scaffolding do site sem precisar digitar informações adicionais do caminho.</p>
	</div>

4. Para criar o scaffolding que será usado para esse aplicativo, use o comando **express**:

        express

	A saída deste comando deve ser semelhante ao seguinte:

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public/javascripts
		create : ./public
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./views
		create : ./views/layout.jade
		create : ./views/index.jade
		create : ./routes
		create : ./routes/index.js
		create : ./routes/user.js
		create : ./public/images

		install dependencies:
		  $ cd . && npm install

		run the app:
		  $ node app

Após a conclusão do comando, você deve ter vários novos diretórios e arquivos no diretório **tasklist**.

###Instalar módulos adicionais

O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, ao implantar esse aplicativo em um site do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para oferecer suporte ao seu aplicativo.

1. Na linha de comando, altere os diretórios para a pasta **tasklist** e digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída deste comando deve ser semelhante ao seguinte:

		express@3.4.0 node_modules\express
		├── methods@0.0.1
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, bytes@0.2.0, qs@0.6.5, multiparty@2.1.8)

		jade@0.35.0 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.0.0
		├── mkdirp@0.3.5
		├── monocle@1.1.50 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── with@1.1.1 (uglify-js@2.4.0)
		└── constantinople@1.0.2 (uglify-js@2.4.0)

	Isso instala todos os módulos padrão de que o Express precisa.

2. Em seguida, digite o seguinte comando para instalar os módulos [azure], [node-uuid], [nconf] e [async] localmente, bem como para salvar uma entrada para eles no arquivo **package.json**:

		npm install azure node-uuid async nconf --save

	A saída deste comando deve ser semelhante ao seguinte:

		async@0.2.9 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.7 node_modules\nconf
		├── ini@1.1.0
		├── async@0.1.22
		├── pkginfo@0.2.3
		└── optimist@0.3.7 (wordwrap@0.0.2)

		azure@0.7.15 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── envconf@0.0.4
		├── node-uuid@1.2.0
		├── mpns@2.0.1
		├── underscore@1.5.2
		├── mime@1.2.11
		├── validator@1.5.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.8 (sax@0.5.5)
		└── request@2.25.0 (json-stringify-safe@5.0.0, aws-sign@0.3.0, forever-agent@0.5.0, tunnel-agent@0.3.0, qs@0.6.5, oauth-sign@0.3.0, cookie-jar@0.3.0, node-uuid@1.4.1, http-signature@0.10.0, form-data@0.1.1, hawk@1.0.0)

##Usando um serviço de Tabela em um aplicativo do nó

Nesta seção, você ampliará o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também modificará o **app.js** existente e criar um novo arquivo **tasklist.js** que usa o modelo.

### Criar o módulo

1. No diretório **tasklist**, crie um novo diretório chamado **models**.

2. No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3. No início do arquivo **task.js**, adicione o seguinte código para fazer a referência às bibliotecas necessárias:

        var azure = require('azure');
  		var uuid = require('node-uuid');

4. Em seguida, você adicionará o código para definir e exportar o objeto da Tarefa. Este objeto é responsável por se conectar à tabela.

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto da Tarefa, que permitem interações com os dados armazenados na tabela:

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(query, function entitiesQueried(error, entities) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, entities);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    item.RowKey = uuid();
		    item.PartitionKey = self.partitionKey;
		    item.completed = false;
		    self.storageClient.insertEntity(self.tableName, item, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(item, callback) {
		    self = this;
		    self.storageClient.queryEntity(self.tableName, self.partitionKey, item, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Salve e feche o arquivo **task.js**.

###Criar o controlador

1. No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2. Adicione o seguinte código a **tasklist.js**. Isso carrega os módulos azure e async, que são usados por **tasklist.js**. Isso também define a função de **TaskList**, que é transmitida a uma instância do objeto da **Tarefa** definido anteriormente:

		var azure = require('azure');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Continue adicionando ao arquivo **tasklist.js**, adicionando os métodos usados para **showTasks**, **addTask** e **completeTasks**:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = azure.TableQuery
		      .select()
		      .from(self.task.tableName)
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this      
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}

3. Salve o arquivo **tasklist.js**.

### Modificar app.js

1. No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.

2. No início do arquivo, adicione o seguinte para carregar o módulo azure, definir o nome da tabela, partitionKey e definir as credenciais de armazenamento usadas por este exemplo:

		var azure = require('azure');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME")
		var partitionKey = nconf.get("PARTITION_KEY")
		var accountName = nconf.get("STORAGE_NAME")
		var accountKey = nconf.get("STORAGE_KEY");

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>nconf carregará os valores de configuração de uma das variáveis de ambiente ou o arquivo **config.json**, que será criado posteriormente.</p>
	</div>

3. No arquivo app.js, role para baixo até onde for capaz de visualizar a seguinte linha:

		app.get('/', routes.index);
		app.get('/users', user.list);

	Substitua as linhas acima pelo código mostrado abaixo. Isso inicializará uma instância de <strong>Tarefa</strong> com uma conexão à sua conta de armazenamento. Isso é transmitido a <strong>TaskList</strong>, que a usará para se comunicar com o serviço de Tabela:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. Salve o arquivo **app.js**.

###Modificar a exibição de índice

1. Altere os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

2. Substitua o conteúdo do arquivo **index.jade** pelo código abaixo. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.

		extends layout

		block content
		  h1= title
		  br

		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      each task in tasks
		        tr
		          td #{task.name}
		          td #{task.category}
		          - var day   = task.Timestamp.getDate();
		          - var month = task.Timestamp.getMonth() + 1;
		          - var year  = task.Timestamp.getFullYear();
		          td #{month + "/" + day + "/" + year}
		          td
		            input(type="checkbox", name="#{task.RowKey}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name: 
		    input(name="item[name]", type="textbox")
		    label Item Category: 
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Salve e feche o arquivo **index.jade**.

###Modificar o layout global

O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar o [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante.

1. Baixe e extraia os arquivos para o [Twitter Bootstrap](http://getbootstrap.com/) Copie o arquivo **bootstrap.min.css** da pasta **bootstrap\\dist\\css** para o diretório **public\\stylesheets** de seu aplicativo de lista de tarefas.

2. Na pasta **views**, abra o **layout.jade** em seu editor de texto e substitua o conteúdo pelo seguinte:

		doctype 5
		html
		  head
		    title= title
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body.app
		    nav.navbar.navbar-default
		      div.navbar-header
		        a.navbar-brand(href='/') My Tasks
		    block content

3. Salve o arquivo **layout.jade**.

###Criar o arquivo de configuração

O arquivo **config.json** contém a cadeia de caracteres de conexão usada para se conectar ao Banco de Dados SQL e que é lida pelo aplicativo durante o tempo de execução. Execute as seguintes etapas para criar esse arquivo:

1. No diretório **tasklist**, crie um novo arquivo chamado **config.json** e abra-o em um editor de texto.

2. O conteúdo do arquivo **config.json** deve ser semelhante ao seguinte:

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	Substitua o **nome da conta de armazenamento** pelo nome da conta de armazenamento criada anteriormente. Substitua a **chave de acesso do armazenamento** pela chave de acesso primário de sua conta de armazenamento.

3. Salve o arquivo.

##Executar o aplicativo localmente

Execute as etapas a seguir para testar o aplicativo em seu computador local.

1. Na linha de comando, altere os diretórios para o diretório **tasklist**.

2. Use o seguinte comando para iniciar o aplicativo localmente:

        node app.js

3. Abra o navegador e acesse http://127.0.0.1:3000. Isso deve exibir uma página semelhante à seguinte:

    ![Uma página da Web que exibe uma lista de tarefas vazia][node-table-finished]

4. Use os campos fornecidos para **Nome do Item** e **Categoria do Item** para inserir informações e clique em **Adicionar item**.

5. A página deverá ser atualizada para exibir o item na tabela da ToDo List.

    ![Uma imagem do novo item na lista de tarefas][node-table-list-items]

6. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir e clicar em **Atualizar tarefas**.

7. Para parar o processo de nó, acesse a linha de comando e pressione as teclas **CTRL** e **C**.

##Implantar seu aplicativo no Azure

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo site do Azure e, em seguida, usa o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

<div class="dev-callout">
<strong>Observação</strong>
<p>Essas etapas também podem ser realizadas com o uso do portal do Azure. Para consultar as etapas sobre como usar o portal do Azure para implantar um aplicativo Node.js, consulte <a href="http://content-ppe.windowsazure.com/pt-br/develop/nodejs/tutorials/create-a-website-(mac)/">Criar e implantar um aplicativo Node.js para um site do Azure</a>.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>Se este for o primeiro site do Azure criado, é necessário usar o portal do Azure para implantar esse aplicativo.</p>
</div>

###Habilitar o recurso do site do Azure

Se você não tiver uma conta do Azure, será possível se inscrever em uma [conta gratuita]. Após a inscrição, siga estas etapas para habilitar o recurso de site do Azure.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Instalar as ferramentas de linha de comando do Azure para Mac e Linux

Para instalar as ferramentas de linha de comando, use o seguinte comando:
	
	npm install azure-cli -g

<div class="dev-callout">
<strong>Observação</strong>
<p>Se você já tiver instalado o **SDK do Azure para o Node.js** do <a href="/pt-br/develop/nodejs/">Centro de Desenvolvedores do Azure</a>, então as ferramentas de linha de comando já devem estar instaladas. Para obter mais informações, consulte <a href="/pt-br/develop/nodejs/how-to-guides/command-line-tools/">Ferramentas de linha de comando do Azure para Mac e Linux</a>.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>Enquanto as ferramentas de linha de comando foram criadas principalmente para usuários do Mac e do Linux, eles se baseiam em Node.js e devem funcionar em qualquer sistema capaz de executar nós.</p>
</div>

###Importar configurações de publicação

Antes de usar as ferramentas de linha de comando com o Azure, você deve primeiro baixar um arquivo que contém informações sobre sua assinatura. Execute as seguintes etapas para fazer o download e importar este arquivo.

1. Na linha de comando, altere os diretórios para o diretório **tasklist**.

2. Digite a seguinte linha de comando para iniciar o navegador e vá até a página de download. Se solicitado, faça o login usando a conta associada à sua assinatura.

		azure account download
	
	![A página de download][download-publishing-settings]
	
	O download do arquivo deve começar automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo.

3. Após o download do arquivo, use o seguinte comando para importar as configurações:

		azure account import <path-to-file>
		
	Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior. Quando o comando for concluído, você verá uma saída semelhante à seguinte:
	
		info:   Executing command account import
		info:   Setting service endpoint to: management.core.windows.net
		info:   Setting service port to: 443
		info:   Found subscription: YourSubscription
		info:   Setting default subscription to: YourSubscription
		warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK

4. Quando a importação for concluída, você deve excluir o arquivo de configurações de publicação, já que ele não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

###Criar um site do Azure

1. Na linha de comando, altere os diretórios para o diretório **tasklist**.

2. Use o comando a seguir para criar um novo site do Azure

		azure site create --git
		
	Você será direcionado para o nome do site e o data center onde o site estará localizado. Forneça um nome exclusivo e selecione o data center geograficamente próximo à sua localidade.
	
	O parâmetro `- git` criará um repositório Git no Azure para este site. Ele também inicializará um repositório Git no diretório atual, se não houver nenhum. Isso também criará um [Git remoto] chamado 'azure', que será usado para publicar o aplicativo no Azure. Por fim, isso criará um arquivo **web.config**, que contém as configurações usadas pelo Azure para hospedar aplicativos de nó.
	
	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se este comando for executado de um diretório que já contém um repositório Git, ele não inicializará novamente o diretório.</p>
	</div>
	
	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se o parâmetro `--git` for omitido, ainda que o diretório contenha um repositório Git, o 'azure' remoto ainda será criado.</p>
	</div>
	
	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha que começa com **Site criado em** contém a URL para o site.
	
		info:   Executing command site create
		help:   Need a site name
		Name: TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se este for o primeiro Site do Azure de sua assinatura, você será instruído a usar o portal para criar o site. Para obter mais informações, consulte <a href="/pt-br/develop/nodejs/tutorials/create-a-website-(mac)/">Criar e implantar um aplicativo Node.js em um site do Azure</a>.</p>
	</div>

###Publicar o aplicativo

1. Na janela do Terminal, altere os diretórios para o diretório **tasklist**, se você não ainda estiver lá.

2. Use os seguintes comandos para adicionar e, em seguida, confirme os arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

3. Ao enviar as alterações do repositório Git mais recentes para o Site do Azure, você deve especificar que a ramificação de destino é **master**, já que ele é usado para o conteúdo do site.

		git push azure master
	
	No final da implantação, você verá uma instrução semelhante ao seguinte:
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Quando a operação de envio for concluída, vá até a URL do site retornada anteriormente pelo comando `azure create site` para exibir o seu aplicativo.

###Alternar para uma variável de ambiente

Anteriormente, implementamos um código que procura uma variável de ambiente **SQL_CONN** para a cadeia de conexão ou carrega o valor do arquivo **config.json**. Nas etapas a seguir você criará um par de chave/valor na configuração de seu site, que o aplicativo real acessa por meio de uma variável de ambiente.

1. No Portal de Gerenciamento, clique em **Sites** e selecione seu site.

	![Abrir o painel do site][go-to-dashboard]

2. Clique em **CONFIGURAR** e localize a seção **configurações do aplicativo** da página. 

	![configurar link][web-configure]

3. Na seção **configurações do aplicativo**, digite **STORAGE_NAME** no campo **KEY** e o nome da sua conta de armazenamento no campo **VALUE**. Clique na marca de seleção para mover para o próximo campo. Repita este processo para as chaves e os valores a seguir:

	* **STORAGE_KEY** - a chave de acesso para a sua conta de armazenamento
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![configurações do aplicativo][app-settings]

4. Finalmente, clique no ícone **SALVAR** na parte inferior da página para confirmar a alteração para o ambiente de tempo de execução.

	![salvar as configurações do aplicativo][app-settings-save]

5. Na linha de comando, altere os diretórios para o diretório **tasklist** e digite o seguinte comando para remover o arquivo **config.json**:

		git rm config.json
		git commit -m "Removing config file"

6. Execute o comando a seguir para implantar as alterações no Azure:

		git push azure master

Depois que as alterações tiverem sido implantadas no Azure, seu aplicativo da web deverá continuar a funcionar como agora, lendo a cadeia de conexão na entrada **configurações do aplicativo**. Para verificar isso, altere o valor para a entrada **STORAGE_KEY** em **configurações do aplicativo** para um valor inválido. Depois que você salvar esse valor, o site deverá falhar devido à configuração da chave de acesso de armazenamento.

##Próximas etapas

Embora as etapas neste artigo descrevam como usar o Serviço de Tabela para armazenar informações, você também pode usar o MongoDB. Consulte [Aplicativo da web Node.js com o MongoDB] para obter mais informações.

##Recursos adicionais

[Ferramenta de linha de comando do Azure para Mac e Linux]    
[Criar e implantar um aplicativo Node.js nos sites do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
[Publicando sites do Azure com o Git]: /pt-br/develop/nodejs/common-tasks/publishing-with-git/
[Centro de Desenvolvedores do Azure]: /pt-br/develop/nodejs/


[nó]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[grátis]: http://windowsazure.com
[Git remoto]: http://git-scm.com/docs/git-remote

[Aplicativo Web Node.js com MongoDB]: /pt-br/develop/nodejs/tutorials/website-with-mongodb-(Mac)/
[Ferramenta de linha de comando do Azure para Mac e Linux]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/
[Criar e implantar um aplicativo Node.js em um site do Azure]: ./web-site-with-mongodb-Mac
[Publicando sites do Azure com o Git]: ../CommonTasks/publishing-with-git
[azure]: https://github.com/WindowsAzure/azure-sdk-for-node


[Portal do Azure]: http://windowsazure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

