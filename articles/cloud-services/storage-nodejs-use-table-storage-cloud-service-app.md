<properties 
	pageTitle="Aplicativo Web com armazenamento de tabela (Node.js) | Microsoft Azure" 
	description="Um tutorial que tem como base o tutorial Aplicativo Web com o Express adicionando os serviços de Armazenamento do Azure e o módulo Azure." 
	services="cloud-services, storage" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="robmcm"/>

# Aplicativo Web do Node.js usando Armazenamento

## Visão geral

Neste tutorial, você ampliará o aplicativo criado no tutorial [Aplicativo Web do Node.js usando o Express] usando as bibliotecas de clientes do Microsoft Azure para o Node.js para trabalhar com serviços de gerenciamento de dados. Você ampliará seu aplicativo para criar um aplicativo de lista de tarefas com base na Web que pode ser implantado no Azure. A lista de tarefas permite que um usuário recupere tarefas, adicione novas tarefas e marque tarefas como concluídas.

Os itens da tarefa são armazenados no Armazenamento do Azure. O Armazenamento do Azure fornece um armazenamento de dados não estruturado altamente disponível e tolerante a falhas. O Armazenamento do Azure inclui diversas estruturas de dados, em que você pode armazenar e acessar dados, além de poder utilizar os serviços de armazenamento das APIs incluídos no SDK do Azure para Node.js ou por meio de APIs REST. Para obter mais informações, consulte [Armazenando e acessando dados no Azure (a página pode estar em inglês)].

Este tutorial pressupõe que você tenha concluído os tutoriais [Aplicativo Web do Node.js] e [Node.js com Express][Node.js Web Application using Express].

Você aprenderá:

-   Trabalhar com o mecanismo de modelo Jade
-   Trabalhar com os serviços de gerenciamento de dados do Azure

Abaixo, uma captura de tela do aplicativo concluído:

![A página da Web completa no Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Definindo credenciais de armazenamento em Web.Config

Para acessar o Armazenamento do Azure, você precisa transmitir as credenciais de armazenamento. Para isso, você deve utilizar as configurações do aplicativo web.config. Essas configurações serão transmitidas como variáveis de ambiente para Node que, em seguida, serão lidas pelo SDK do Azure.

> [AZURE.NOTE] As credenciais de armazenamento são usadas somente quando o aplicativo é implantado no Azure. Quando executado no emulador, o aplicativo usará o emulador de armazenamento.

Execute as etapas a seguir para recuperar as credenciais da conta de armazenamento e adicioná-las às configurações de web.config:

1.  Se ainda não estiver aberto, inicie o PowerShell do Azure no menu **Iniciar** expandindo **Todos os Programas, Azure** e clicando com o botão direito do mouse em **PowerShell do Azure** e, em seguida, selecione **Executar como Administrador**.

2.  Altere os diretórios para a pasta que contém o aplicativo. Por exemplo, C:\\node\\tasklist\\WebRole1.

3.  Na janela do PowerShell do Azure, insira o seguinte cmdlet para recuperar as informações da conta de armazenamento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	Isso recupera a lista de contas de armazenamento e as chaves da conta associada ao seu serviço hospedado.

	> [AZURE.NOTE] Como o SDK do Azure cria uma conta de armazenamento quando você implanta um serviço, uma conta de armazenamento já deve existir da implantação de seu aplicativo nos guias anteriores.

4.  Abra o arquivo **ServiceDefinition.csdef** que contém as configurações de ambiente usadas quando o aplicativo é implantado no Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Insira o seguinte bloco sob o elemento **Environment**, substituindo {STORAGE ACCOUNT} e {STORAGE ACCESS KEY} pelo nome da conta e a chave primária da conta de armazenamento que deseja usar para a implantação:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

	![O conteúdo do arquivo web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salve o arquivo e feche o Bloco de Notas.

### Instalar módulos adicionais

2. Use o seguinte comando para instalar os módulos azure, [node-uuid], [nconf] e [async] localmente, bem como para salvar uma entrada para eles no arquivo **package.json**:

		PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

	A saída desse comando deve ser semelhante ao seguinte:

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.9 node_modules\nconf
		├── ini@1.1.0
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##Usando um serviço de Tabela em um aplicativo de nó

Nesta seção, você ampliará o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também modificará o **app.js** existente e criar um novo arquivo **tasklist.js** que usa o modelo.

### Criar o modelo

1. No diretório **WebRole1**, crie um novo diretório com nome **models**.

2. No diretório **models**, crie um novo arquivo com nome **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

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
		    self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

### Criar o controlador

1. No diretório **WebRole1/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

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
		    var query = azure.TableQuery()
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

1. No diretório **WebRole1**, abra o arquivo **app.js** em um editor de texto. 

2. No início do arquivo, adicione o seguinte para carregar o módulo azure e definir o nome da tabela e a chave de partição:

		var azure = require('azure-storage');
		var tableName = 'tasks';
		var partitionKey = 'hometasks';

3. No arquivo app.js, role para baixo até onde for capaz de visualizar a seguinte linha:

		app.use('/', routes);
		app.use('/users', users);

	Substitua as linhas acima pelo código mostrado abaixo. Será inicializada uma instância de <strong>Tarefa</strong> com uma conexão à sua conta de armazenamento. Ela será transmitida à <strong>TaskList</strong>, que a usará para se comunicar com o serviço Tabela:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. Salve o arquivo **app.js**.

### Modificar a exibição de índice

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
		      if tasks != []
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

### Modificar o layout global

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

### Executando o aplicativo no emulador

Use o seguinte comando para iniciar o aplicativo no emulador.

	PS C:\node\tasklist\WebRole1> start-azureemulator -launch

O navegador abrirá e exibirá a página a seguir:

![Uma página da Web chamada My Task List com uma tabela que contém tarefas e campos para adicionar uma nova tarefa.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Use o formulário para adicionar itens ou remover itens existentes marcando-os como completos.

## Publicando o aplicativo no Azure


Na janela do Windows PowerShell, chame o seguinte cmdlet para reimplantar o serviço hospedado no Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Substitua **myuniquesitename** por um nome exclusivo para o aplicativo. Substitua **datacentername** pelo nome de um data center do Azure, como **West US**.

Após a conclusão da implantação, você deverá ver uma resposta semelhante ao seguinte:

	PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
	WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
	WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
	WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
	WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
	WARNING: 2:19:01 PM - Connecting...
	WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
	WARNING: 2:19:40 PM - Upgrading...
	WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
	WARNING: 2:22:48 PM - Initializing...
	WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
	WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Da mesma maneira que antes, como você especificou a opção **-launch**, o navegador será aberto e exibirá o aplicativo em execução no Azure quando a publicação for concluída.

![Uma janela do navegador exibindo a página My Task List. A URL indica que a página agora está sendo hospedada no Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Parando e excluindo o aplicativo

Depois de implantar o aplicativo, você talvez queira desabilitá-lo, de forma que seja possível evitar os custos ou compilar e implantar outros aplicativos no período de avaliação gratuita.

O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido quando seu aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.

As etapas a seguir mostram como parar e excluir o aplicativo.

1.  Na janela do Windows PowerShell, interrompa a implantação do serviço criada na seção anterior com o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	Interromper o serviço pode levar alguns minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que foi interrompido.

3.  Para excluir o serviço, chame o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	Quando solicitado, insira **Y** para excluir o serviço.

	Excluir o serviço pode levar alguns minutos. Após o serviço ter sido excluído, você recebe uma mensagem indicando que o serviço foi excluído.

  [Node.js Web Application using Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Aplicativo Web do Node.js usando o Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Armazenando e acessando dados no Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Aplicativo Web do Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 

<!---HONumber=AcomDC_0309_2016-->