<properties 
	pageTitle="Criar um aplicativo Web Node.js no Azure com o MongoDB em uma Máquina Virtual" 
	description="Como usar o MongoDB para armazenar dados em um aplicativo Node.js hospedado no Azure."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson"/>


# Criar um aplicativo Web Node.js no Azure com o MongoDB em uma Máquina Virtual

Este tutorial mostra como usar o [MongoDB] hospedado em uma Máquina Virtual do Azure para hospedar dados e acessar os dados de um aplicativo de [nó] hospedado em Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). [O MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho.

Você aprenderá a:

* Como configurar uma máquina virtual executando o Ubuntu e o MongoDB no Repositório de VM.
* Como acessar o MongoDB de um aplicativo de nó
* Como usar as ferramentas de multiplataforma do Azure para criar um aplicativo Web no Serviço de Aplicativo do Azure

Seguindo este tutorial, você criará um aplicativo simples de gerenciamento de tarefas baseado na web que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas no MongoDB.

> [AZURE.NOTE]Este tutorial usa uma instância do MongoDB instalado em uma máquina virtual. Se você preferir usar uma instância do MongoDB hospedada fornecida pela MongoLabs, consulte [Criar um aplicativo Web Node.js no Azure com o MongoDB usando o complemento da MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb).
 
Os arquivos do projeto deste tutorial serão armazenados em um diretório chamado **tasklist**, e o aplicativo completo terá um aspecto semelhante ao seguinte:

![Uma página da Web que exibe uma lista de tarefas vazia][node-mongo-finished]

> [AZURE.NOTE]Muitas das etapas abaixo mencionam o uso da linha de comando. Para essas etapas, use a linha de comando de seu sistema operacional, como **Windows PowerShell** (Windows) ou **Bash** (Unix Shell). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo Terminal.

##Pré-requisitos

As etapas deste tutorial usam o Node.js. É necessário ter uma versão recente do [Node.js][node] em seu ambiente de desenvolvimento.

Além disso, o [Git] deve estar disponível na linha de comando no ambiente de desenvolvimento, uma vez que é usado para implantar o aplicativo em um Site do Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Criar uma máquina virtual

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Embora seja possível criar uma nova VM e, em seguida, instalar o MongoDB nela seguindo os [guias de instalação do MongoDB][installguides], uma VM pré-instalada com o MongoDB está disponível no Azure Marketplace. As etapas a seguir demonstram como usar um dos muitos modelos de VM.

> [AZURE.NOTE]A imagem da comunidade usada por este tutorial armazena os dados do MongoDB no disco OS. Embora isso seja suficiente para o propósito do tutorial, a armazenagem de dados do MongoDB em um disco de dados fornecerá um maior desempenho. Para as etapas sobre criar uma MV nova, incluindo o disco de dados, e armazenagem de dados do MongoDB no disco de dados, consulte [Instalar o MongoDB no Linux no Azure][mongodbonazure].

1. Faça logon no [Portal do Azure][azureportal].

3. Clique em **Novo** > **Dados + Armazenamento** > **Marketplace**.

	![captura de tela da seleção de Repositório de VM][selectdepo]

2. Na caixa de pesquisa na parte superior, digite "mongodb" e selecione **MongoDB v 2.2.3 no Hardened Ubuntu 12.04 LTS**. Clique em **Criar** para continuar.

	![captura de tela do mongodb v2.2.3 selecionado na imagem do Ubuntu protegida.][selectedimage]

	Clique na seta na parte inferior para prosseguir para a próxima tela.

7. Configure o **Nome de Host** da VM, o **Nome de Usuário** e a **Senha** do administrador e o **Grupo de Recursos**. Em seguida, clique em **Configuração Opcional**.

	![captura de tela da configuração da vm][vmconfig]

8. Defina pontos de extremidade adicionais para a máquina virtual. Como iremos acessar o MongoDB nessa VM, adicione um novo ponto de extremidade usando as seguintes informações:

	* Nome – MongoDB
	* Protocolo – TCP
	* Porta pública – 27017
	* Porta privada – 27017

	Para expor o portal da web do MongoDB, adicione outro ponto de extremidade usando as seguintes informações:

	* Nome – MongoDBWeb
	* Protocolo – TCP
	* Porta pública – 28017
	* Porta privada – 28017
	
	![captura de tela da configuração do ponto de extremidade][vmendpoint]

9. Clique em **OK** duas vezes e, em seguida, clique em **Criar** para criar a VM.

	Depois de criar a máquina virtual, você a verá em seu Quadro Inicial e poderá clicar nela para abrir a folha da VM. Você deverá ser capaz de abrir um navegador da Web para **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** para verificar se o MongoDB está em execução. Na parte inferior da página deve haver um log que exibe informações sobre o serviço, semelhante ao seguinte:

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Se o log exibir erros, consulte a [Documentação do MongoDB][mongodocs] para obter as etapas de solução de problemas.


##Instalar módulos e gerar scaffolding

Nesta seção você criará um novo aplicativo de nó em nosso ambiente de desenvolvimento e usará o npm para adicionar pacotes de módulo. Para o aplicativo de lista de tarefas você usará os módulos [Express] e [Mongoose]. O módulo Express fornece uma estrutura de Modelo-Exibição-Controlador para nós, enquanto Mongoose é um driver para a comunicação com o MongoDB.

###Instalar o express e gerar scaffolding

1. Da linha de comando, mude os diretórios para o diretório **tasklist**. Se o diretório **tasklist** não existir, crie-o.

	> [AZURE.NOTE]Este tutorial faz referência à pasta **tasklist**. O caminho completo da pasta é omitido, pois a semântica de caminho difere entre os sistemas operacionais. Você deve criar essa pasta em um local de fácil acesso em seu sistema de arquivos local, como **\~/node/tasklist** ou **c:\\node\\tasklist**

2. Digite o seguinte comando para instalar o comando Express.

	npm install express-generator -g
 
	> [AZURE.NOTE]Ao usar o parâmetro '-g' em alguns sistemas operacionais, você poderá receber uma mensagem de ___Erro: EPERM, chmod '/usr/local/bin/express'___ e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando `sudo` para executar o npm com um nível de privilégio mais elevado.

    A saída desse comando deve ser semelhante ao seguinte:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [AZURE.NOTE]O parâmetro '-g' usado durante a instalação do módulo expresso o instala globalmente. Isso é feito para que possamos acessar o comando ___express___ para gerar o scaffolding do aplicativo sem precisar digitar informações adicionais de caminho.

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

3. Copiar o arquivo **tasklist/bin/www** para um arquivo nomeado **Serve.js** na pasta **tasklist**. Os Aplicativos Web do Serviço de Aplicativo esperam o que ponto de entrada para um aplicativo Node.js seja **server.js** ou **app.js**. Como o **app.js** já existe, mas não é o ponto de entrada, devemos usar o **server.js**.

4. Modifique o **server.js** para remover um dos caracteres '.' da seguinte linha.

		var app = require('../app');

	A linha modificada deve aparecer como segue.

		var app = require('./app');

	Isso é requerido porque o **server.js** (anteriormente **bin/www**,) está agora na mesma pasta que o arquivo **app.js** requerido.

###Instalar módulos adicionais

O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, quando você implantar esse aplicativo nos Aplicativos Web do Serviço de Aplicativo, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para oferecer suporte ao seu aplicativo.
	
1. Na pasta **tasklist**, use o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída desse comando deve ser semelhante ao seguinte:

		debug@0.7.4 node_modules\debug
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)
		
		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                                                

	Isso instala todos os módulos padrão usados pelo aplicativo Express.

2. Em seguida, digite o seguinte comando para instalar o módulo Mongoose localmente, bem como para salvar uma entrada para ele no arquivo **package.json**:

		npm install mongoose --save

	A saída desse comando deve ser semelhante ao seguinte:

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)         

    > [AZURE.NOTE]Você pode ignorar com segurança quaisquer mensagens sobre a instalação do analisador de bson C++.

##Usando MongoDB em um aplicativo de nó

Nesta seção, você ampliará o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também vai modificar o **app.js** existente e criar um novo arquivo de controlador **tasklist.js** para usar o modelo.

### Criar o modelo

1. No diretório **tasklist**, crie um novo diretório com nome **models**.

2. No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3. No início do arquivo **task.js**, adicione o seguinte código para fazer a referência às bibliotecas necessárias:

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. Em seguida, você adicionará código para definir e exportar o modelo. Este modelo será usado para executar as interações com o banco de dados MongoDB.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Salve e feche o arquivo **task.js**.

###Criar o controlador

1. No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2. Adicione o seguinte código ao **tasklist.js**. Isso carrega o módulo mongoose e o modelo da tarefa definido em **task.js**. A função TaskList é usada para criar a conexão com o servidor MongoDB com base no valor de **connection**:

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Continue adicionando ao arquivo **tasklist.js**, adicionando os métodos usados para **showTasks**, **addTask** e **completeTasks**:

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
      		  if(err) {
      		    throw err;
      		  }
    	    });
    	  	res.redirect('/');
  		  },
  

  		  completeTask: function(req,res) {
    		var completedTasks = req.body;
    		for(taskId in completedTasks) {
      		  if(completedTasks[taskId]=='true') {
        		var conditions = { _id: taskId };
        		var updates = { itemCompleted: completedTasks[taskId] };
        		task.update(conditions, updates, function updatedTask(err) {
          		  if(err) {
          		    throw err;
          		  }
        		});
      		  }
    		}
    		res.redirect('/');
  		  }
		}

3. Salve o arquivo **tasklist.js**.

### Modificar app.js

1. No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.

2. Adicione o seguinte código ao início do arquivo **app.js**. Isso inicializará o **TaskList** com a cadeia de conexão para o servidor do MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Observe a segunda linha; você acessa uma variável de ambiente que você vai configurar mais tarde, que contém as informações de conexão para a instância mongo. Se você tiver uma instância local do mongo em execução para fins de desenvolvimento, é aconselhável definir temporariamente esse valor para "localhost" em vez de process.env.MONGODB_URI.

3. Encontre as linhas a seguir:

		app.use('/', routes);
		app.use('/users', users);

	Substitua as duas linhas acima com o seguinte:

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	Isso adiciona as funções definidas no **tasklist.js** como rotas.

4. Salve o arquivo **app.js**.

###Modificar a exibição de índice

1. Altere os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

2. Substitua o conteúdo do arquivo **index.jade** pelo código abaixo. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.

		h1= title
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1") 
		    tr
		      td Item Name: 
		      td 
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. Salve e feche o arquivo **index.jade**.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Implantar seu aplicativo no Azure

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo aplicativo Web no Serviço de Aplicativo do Azure e, em seguida, usam o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

> [AZURE.NOTE]Essas etapas também podem ser executadas com o uso do Portal do Azure. Para obter as etapas sobre como usar o Portal do Azure para implantar um aplicativo Node.js, consulte [Criar e implantar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-develop-deploy-mac.md).

> [AZURE.NOTE]Se esse for o primeiro aplicativo Web do Serviço de Aplicativo que você criar, use o Portal do Azure para implantar esse aplicativo.

###Instalar a interface de linha de comando entre plataformas do Azure

A Interface de linha de comando entre plataformas do Azure (xplat-cli) permite que você execute operações de gerenciamento para serviços do Azure. Se você ainda não tiver instalado e configurado o xplat-cli no ambiente de desenvolvimento, consulte [Instalar e configurar a Interface de linha de comando entre plataformas do Azure][xplatcli] para obter instruções.

###Criar um aplicativo Web do Serviço de Aplicativo

1. Da linha de comando, mude os diretórios para o diretório **tasklist**.

2. Use o comando a seguir para criar um novo aplicativo Web do Serviço do Aplicativo. Substitua 'myuniqueappname' por um nome exclusivo para seu aplicativo Web. Esse valor é usado como parte da URL do aplicativo Web resultante.

		azure site create myuniqueappname --git
		
	Será solicitado o data center em que o aplicativo Web estará localizado. Selecione o data center geograficamente próximo de sua localidade.
	
	O parâmetro `--git` criará um repositório Git localmente na pasta **tasklist**, se não houver nenhum. Isso também criará um [Git remoto] chamado 'azure', que será usado para publicar o aplicativo no Azure. Isso criará um [iisnode.yml], que contém as configurações usadas pelo Azure para hospedar aplicativos de nó. Finalmente, também criará um arquivo .gitignore para excluir a pasta node-modules da publicação no .git.
	
	> [AZURE.NOTE]Se este comando for executado de um diretório que já contém um repositório Git, ele não inicializará novamente o diretório.
	
	> [AZURE.NOTE]Se o parâmetro '--git' for omitido, ainda que o diretório contenha um repositório Git, o 'azure' remoto ainda será criado.
	
	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha que começa com **Site criado em** contém a URL para o aplicativo Web do Serviço de Aplicativo.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> Se esse for o primeiro aplicativo Web do Serviço de Aplicativo para sua assinatura, você será instruído a usar o portal para criar o aplicativo Web. Para obter mais informações, consulte [Criar e implantar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-develop-deploy-mac.md).

###Definir a variável de ambiente MONGODB_URI

O aplicativo espera a cadeia de conexão para instância do MongoDB para ficarem disponíveis na variável de ambiente do .MONGODB_URI. Para definir este valor para o aplicativo Web, use o seguinte comando:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Isso criará uma nova configuração de aplicativo para o aplicativo Web, que será usada para preencher a variável de ambiente do MONGODB_URI lida pelo aplicativo Web. Substitua o valor de 'mymongodb.cloudapp.net' pelo nome da máquina virtual onde o MongoDB foi instalado.

###Publicar o aplicativo

1. Na janela do Terminal, altere os diretórios para o diretório **tasklist**, se você não ainda estiver lá.

2. Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

3. Ao enviar as alterações mais recentes do repositório Git ao aplicativo Web do Serviço de Aplicativo, você deve especificar que a ramificação de destino é **master**, pois isso é usado para o conteúdo do aplicativo Web.

		git push azure master
	
	Você verá saídas semelhantes ao seguinte. Durante a implantação, o Azure baixará todos os módulos npm.

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master
 
4. Quando a operação de envio for concluída, navegue para o aplicativo Web usando o comando `azure site browse` para exibir seu aplicativo.

##Próximas etapas

Embora as etapas deste artigo descrevam como usar o MongoDB para armazenar informações, você também pode usar o serviço Tabela do Azure. Consulte [Aplicativo Web Node.js usando o Serviço de Tabela do Azure] para obter mais informações.

Para saber como usar uma instância hospedada do MongoDB fornecida pela MongoLab, consulte [Criar um aplicativo Web Node.js no Azure com o MongoDB usando o complemento da MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb.md).

Para saber como proteger o MongoDB, consulte [Segurança do MongoDB][mongosecurity].

##Recursos adicionais

[Ferramenta de linha de comando do Azure para Mac e Linux][Build and deploy a Node.js web app in Azure App Service] [Implantação contínua usando GIT no Serviço de Aplicativo do Azure]

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[nó]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[O MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Implantação contínua usando GIT no Serviço de Aplicativo do Azure]: web-sites-publish-source-control.md
[Aplicativo Web Node.js usando o Serviço de Tabela do Azure]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
<!--HONumber=54-->