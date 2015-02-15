<properties 
	pageTitle="Site do Node.js com armazenamento de tabela | Microsoft Azure" 
	description="Um tutorial que ensina a usar o serviço Tabela do Azure para armazenar dados de um aplicativo de nó hospedado em um site do Azure." 
	services="web-sites, storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# Aplicativo Web Node.js que usa o Serviço Tabela do Azure

Este tutorial mostra como usar o serviço de Tabela fornecido pelo Gerenciamento de Dados do Azure para armazenar e acessar dados em um aplicativo de [nó] hospedado no Azure. Este tutorial pressupõe que você tenha alguma experiência anterior usando nó e [Git].

Você aprenderá:

* Como usar npm (gerenciador de pacote de nós) para instalar os módulos do nó

* Como trabalhar com o serviço Tabela do Azure

* Como usar a ferramenta de linha de comando do Azure para Mac e Linux para criar um site do Azure

Seguindo este tutorial, você criará um aplicativo simples de gerenciamento de tarefas baseado na web que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas no serviço Tabela.

Os arquivos do projeto deste tutorial serão armazenados em um diretório chamado **tasklist**, e o aplicativo completo terá um aspecto semelhante ao seguinte:

![A web page displaying an empty tasklist][node-table-finished]

> [AZURE.NOTE] Este tutorial faz referência à pasta **tasklist**. O caminho completo da pasta é omitido, pois a semântica de caminho difere entre os sistemas operacionais. Você deve criar essa pasta em um local de fácil acesso em seu sistema de arquivos local, como **~/node/tasklist** ou **c:\node\tasklist**.

> [AZURE.NOTE] Muitas das etapas abaixo mencionam o uso da linha de comando. Para essas etapas, use a linha de comando de seu sistema operacional, como **cmd.exe** (Windows) ou **Bash** (Unix Shell). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo Terminal.

##Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

* Versão do [nó] 0.10.24 ou superior

* [Git]

* Um editor de texto

* Um navegador da Web

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Criar uma conta de armazenamento

Você pode criar uma conta de armazenamento seguindo essas etapas. Essa conta será usada em instruções subsequentes neste tutorial.

1. Abra seu navegador e vá ate o [Portal do Azure]. Se solicitado, faça logon com as informações de assinatura do Azure.

2. Clique em **+ NOVO** na parte inferior da página e selecione **Conta de Armazenamento**.

	![+new][portal-new]

	![storage account][portal-storage-account]

3. Selecione **Criação Rápida** e depois insira a URL e Região/Afinidade para esta conta de armazenamento. Já que esse é um tutorial e não precisa ser replicado globalmente, desmarque **Habilitar Replicação Geográfica**. Por fim, clique em "Criar Conta de Armazenamento".

	![quick create][portal-quick-create-storage]

	Anote a URL que você digitar, pois ela será referenciada como o nome da conta, em etapas posteriores.

4. Depois que a conta de armazenamento tiver sido criada, clique em **Gerenciar chaves** na parte inferior da página. Isso exibirá as teclas de acesso primário e secundário para esta conta de armazenamento. Copie e salve a chave de acesso primário e clique na marca de seleção.

	![access keys][portal-storage-access-keys]

##Instalar módulos e gerar scaffolding

Nesta seção você criará um novo aplicativo de nó e usará o npm para adicionar pacotes de módulo. Para o aplicativo da lista de tarefas, você usará os módulos [Express] e [Azure]. O módulo Express fornece uma estrutura de Controlador da Exibição do Modelo para o nó, enquanto que os módulos do Azure fornecem a conectividade ao serviço Tabela.

###Instalar o express e gerar scaffolding

1. Na linha de comando, altere os diretórios para o diretório **tasklist**. Se o diretório **tasklist** não existir, crie-o.

2. Digite o seguinte para instalar o comando express.

		npm install express-generator@4.2.0 -g

    > [AZURE.NOTE] Ao usar o parâmetro '-g' em alguns sistemas operacionais, você poderá receber uma mensagem de **Erro: EPERM, chmod '/usr/local/bin/express'** e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando **sudo** para executar o npm com um nível de privilégio mais elevado.

    A saída desse comando deve ser semelhante ao seguinte:

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE] O parâmetro '-g' usado durante a instalação do módulo expresso o instala globalmente. Isso é feito para que possamos acessar o comando **express** para gerar o scaffolding do site sem precisar digitar informações adicionais de caminho.**

4. Para criar o scaffolding que será usado para esse aplicativo, use o comando **express**:

        express

	A saída desse comando deve ser semelhante ao seguinte:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www
		
		   install dependencies:
		     $ cd . && npm install
		
		   run the app:
		     $ DEBUG=my-application ./bin/www

	Após a conclusão do comando, você deve ter vários novos diretórios e arquivos no diretório **tasklist**.

###Instalar módulos adicionais

O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, ao implantar esse aplicativo em um site do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo.

1. Na linha de comando, altere os diretórios para a pasta **tasklist** e digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída desse comando deve ser semelhante ao seguinte:

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	Isso instala todos os módulos padrão de que o Express precisa.

2. Em seguida, digite o seguinte comando para instalar os módulos [azure], [node-uuid], [nconf] e [async] localmente, bem como para salvar uma entrada para eles no arquivo **package.json**:

		npm install azure-storage node-uuid async nconf --save

	A saída desse comando deve ser semelhante ao seguinte:

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##Usando um serviço Tabela em um aplicativo de nó

Nesta seção, você ampliará o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também modificará o **app.js** existente e criar um novo arquivo **tasklist.js** que usa o modelo.

### Criar o modelo

1. No diretório **tasklist**, crie um novo diretório chamado **models**.

2. No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3. No início do arquivo **task.js**, adicione o seguinte código para fazer a referência às bibliotecas necessárias:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Em seguida, você adicionará o código para definir e exportar o objeto da tarefa. Este objeto é responsável por se conectar à tabela.

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
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
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

2. Adicione os seguintes códigos ao **tasklist.js**. Isso carrega os módulos azure e async, que são usados por **tasklist.js**. Isso também define a função de **TaskList**, que é transmitida a uma instância do objeto da **Tarefa** definido anteriormente:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Continue adicionando ao arquivo **tasklist.js**, adicionando os métodos usados para **showTasks**, **addTask** e **completeTasks**:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
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

2. No início do arquivo, adicione o seguinte para carregar o módulo azure, defina o nome da tabela, partitionKey e defina as credenciais de armazenamento usadas por este exemplo:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE] nconf carregará os valores de configuração de uma das variáveis de ambiente ou o arquivo **config.json**, que será criado posteriormente.

3. No arquivo app.js, role para baixo até onde for capaz de visualizar a seguinte linha:

		app.use('/', routes);
		app.use('/users', users);

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

1. Mude os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

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
		      if (typeof tasks === "undefined")
		        tr
		          td 
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
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

O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar a [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante.

1. Baixe e extraia os arquivos para a [Twitter Bootstrap](http://getbootstrap.com/). Copie o arquivo **bootstrap.min.css** da pasta **bootstrap\\dist\\css** para o diretório **public\\stylesheets** de seu aplicativo de lista de tarefas.

2. Na pasta **views**, abra o **layout.jade** em seu editor de texto e substitua o conteúdo pelo seguinte:

		doctype html
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

##Execute seu aplicativo localmente

Execute as etapas a seguir para testar o aplicativo em seu computador local:

1. Na linha de comando, altere os diretórios para o diretório **tasklist**.

2. Use o seguinte comando para iniciar o aplicativo localmente:

        npm start

3.  Abra o navegador e acesse http://127.0.0.1:3000. Isso deve exibir uma página da Web semelhante à seguinte:

    ![A webpage displaying an empty tasklist][node-table-finished]

4. Use os campos fornecidos para **Nome do Item** e **Categoria do Item** para inserir informações e clique em **Adicionar item**.

5. A página deverá ser atualizada para exibir o item na tabela da ToDo List.

    ![An image of the new item in the list of tasks][node-table-list-items]

6. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir e clicar em **Atualizar tarefas**.

7. Para parar o processo de nó, acesse a linha de comando e pressione as teclas **CTRL** e **C**.

##Implantar seu aplicativo no Azure

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo site do Azure e, em seguida, usa o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

> [AZURE.NOTE] Essas etapas também podem ser executadas com o uso do portal do Azure. Para obter as etapas sobre como usar o portal do Azure para implantar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Node.js em um site do Azure].

> [AZURE.NOTE] Se este for o primeiro site do Azure que você criou, você deve usar o portal do Azure para implantar este aplicativo.

###Criar uma assinatura do Azure

Se você não tiver uma conta do Azure, será possível se inscrever em uma [conta gratuita]. Após a inscrição, siga estas etapas para continuar esse tutorial.

[AZURE.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Instalar a ferramenta de linha de comando do Azure para Mac e Linux

Para instalar as ferramentas de linha de comando, use o seguinte comando:
	
	npm install azure-cli -g

> [AZURE.NOTE] Para obter mais informações, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](/pt-br/documentation/articles/xplat-cli/);

> [AZURE.NOTE] Enquanto as ferramentas de linha de comando foram criadas principalmente para usuários do Mac e do Linux, eles se baseiam em Node.js e devem funcionar em qualquer sistema capaz de executar nós.

###Importar configurações de publicação

Antes de usar as ferramentas de linha de comando com o Azure, você deve primeiro baixar um arquivo que contém informações sobre sua assinatura. Execute as seguintes etapas para baixar e importar este arquivo.

1. Na linha de comando, altere os diretórios para o diretório **tasklist**.

2. Digite a seguinte linha de comando para iniciar o navegador e vá até a página de download. Se solicitado, faça logon usando a conta associada à sua assinatura.

		azure account download
	
	![The download page][download-publishing-settings]
	
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
		
	Você será solicitado a fornecer o nome do site e o data center onde o site estará localizado. Forneça um nome exclusivo e selecione o data center geograficamente próximo à sua localidade.
	
	O parâmetro `--git` criará um repositório Git no Azure para este site. Ele também inicializará um repositório Git no diretório atual, se não houver nenhum. Isso também criará um [Git remoto] nomeado  'azure', que será usado para publicar o aplicativo no Azure. Por fim, isso criará um arquivo **web.config**, que contém as configurações usadas pelo Azure para hospedar aplicativos de nó.
	
	> [AZURE.NOTE] Se este comando for executado de um diretório que já contém um repositório Git, ele não inicializará novamente o diretório.
	
	> [AZURE.NOTE] Se o parâmetro '`--git`' for omitido, ainda que o diretório contenha um repositório Git, o 'azure' remoto ainda será criado.
	
	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha que começa com **Site criado em** contém a URL para o site.
	
		info:   Executing command site create
		help:   Need a site name
		Name:   TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE] Se este for o primeiro Site do Azure de sua assinatura, você será instruído a usar o portal para criar o site. Para obter mais informações, consulte [Criar e implantar um aplicativo Node.js em um site do Azure].

###Publicar o aplicativo

1. Na janela do Terminal, altere os diretórios para o diretório **tasklist**, se você não ainda estiver lá.

2. Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

3. Ao enviar as mudanças do repositório Git mais recentes para o Site do Azure, você deve especificar que a ramificação de destino é **master**, já que ela é usado para o conteúdo do site.

		git push azure master
	
	No final da implantação, você verá uma instrução semelhante ao seguinte:
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Quando a operação de envio for concluída, vá até a URL do site retornada anteriormente pelo comando  `azure create site` para exibir o seu aplicativo.

###Alternar para uma variável de ambiente

Anteriormente, implementamos um código que procura variáveis de ambiente ou carrega o valor do arquivo **config.json**. Nas etapas a seguir você criará pares de chave/valor na configuração de seu site, que o aplicativo real acessa por meio de uma variável de ambiente.

1. No Portal de Gerenciamento, clique em **Sites** e selecione seu site.

	![Open website dashboard][go-to-dashboard]

2. Clique em **CONFIGURAR** e localize a seção **configurações do aplicativo** da página. 

	![configure link][web-configure]

3. Na seção **configurações do aplicativo**, digite **STORAGE_NAME** no campo **CHAVE** e o nome da sua conta de armazenamento no campo **VALOR**. Clique na marca de seleção para mover para o próximo campo. Repita este processo para as chaves e os valores a seguir:

	* **STORAGE_KEY** - a chave de acesso de sua conta de armazenamento
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![app settings][app-settings]

4. Finalmente, clique no ícone **SALVAR** na parte inferior da página para confirmar a alteração para o ambiente de tempo de execução.

	![app settings save][app-settings-save]

5. Na linha de comando, altere os diretórios para o diretório **tasklist** e digite o seguinte comando para remover o arquivo **config.json**:

		git rm config.json
		git commit -m "Removing config file"

6. Execute o comando a seguir para implantar as alterações no Azure:

		git push azure master

Depois que as alterações tiverem sido implantadas no Azure, seu aplicativo da web deverá continuar a funcionar como agora, lendo a cadeia de conexão na entrada **configurações do aplicativo**. Para verificar isso, altere o valor da entrada **SQL_CONN** em **configurações do aplicativo** para um valor inválido. Depois que você salvar esse valor, o site deverá falhar devido à configuração da chave de acesso de armazenamento inválida.

##Próximas etapas

Embora as etapas neste artigo descrevam como usar o Serviço Tabela para armazenar informações, você também pode usar o MongoDB. Consulte [Aplicativo da web Node.js com o MongoDB] para obter mais informações.

##Recursos adicionais

[Ferramenta de linha de comando do Azure para Mac e Linux]    
[Create and deploy a Node.js application to Azure Web Sites]: /pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Publishing to Azure Web Sites with Git]: /pt-br/documentation/articles/web-sites-publish-source-control/
[Azure Developer Center]: /pt-br/develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remote]: http://git-scm.com/docs/git-remote

[Aplicativo da web Node.js com o MongoDB]: /pt-br/documentation/articles/web-sites-nodejs-store-data-mongodb/
[Ferramenta de linha de comando do Azure para Mac e Linux]: /pt-br/documentation/articles/xplat-cli/

[Publishing to Azure Web Sites with Git]: /pt-br/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


[Azure Portal]: http://windowsazure.com


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

[Create and deploy a Node.js application to an Azure Web Site]: /pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<!--HONumber=42-->
