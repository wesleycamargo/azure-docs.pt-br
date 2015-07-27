<properties
	pageTitle="Criar um aplicativo Web do Node.js no Azure com o MongoDB usando o complemento MongoLab"
	description="Saiba como criar um aplicativo Web do Node. js no Serviço de Aplicativo do Azure que se conecta a uma instância do MongoDB hospedada no MongoLab."
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# Criar um aplicativo Web do Node.js no Azure com o MongoDB usando o complemento MongoLab


<p><em>Por Eric Sedor, MongoLab</em></p>

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1. [Provisionar o banco de dados][provision] - O complemento Azure Marketplace [MongoLab](http://mongolab.com) fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
2. [Criar o aplicativo][create] - Esse será um aplicativo simples do Node. js para manter uma lista de tarefas.
3. [Implantar o aplicativo][deploy] - Vinculando alguns ganchos de configuração, enviaremos o nosso código por push para o [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) rapidamente.
4. [Gerenciar o banco de dados][manage] - finalmente, mostraremos o portal de gerenciamento de banco de dados baseado na web do MongoLab, onde você poderá pesquisar, visualizar e modificar dados facilmente.

A qualquer momento durante este tutorial, fique à vontade para mandar um e-mail para [support@mongolab.com](mailto:support@mongolab.com) em caso de dúvidas.

Antes de continuar, certifique-se de que você tem o seguinte instalado:

* [Node.js] Versão 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Início rápido
Se você tiver alguma familiaridade com o Azure Store, use esta seção para fazer um início rápido. Caso contrário, vá para [Provisionar o banco de dados][provision] abaixo.

1. Abra o Azure Marketplace clicando em **Novo** > **Marketplace**.  
<!-- ![Store][button-store] -->
2. Clique no complemento **MongoLab**. ![MongoLab][entry-mongolab]
3. Clique no complemento **MongoLab** na lista Complementos e, em seguida, clique em **Informações de Conexão**. ![ConnectionInfoButton][button-connectioninfo]  
4. Copie o **MONGOLAB_URI** para a sua área de transferência. ![ConnectionInfoScreen][screen-connectioninfo]

	>[AZURE.NOTE]Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.

5. Adicione o valor à lista **Cadeias de conexão** no menu **Configuração** do seu aplicativo Web no Serviço de Aplicativo do Azure: ![WebAppConnectionStrings][focus-website-connectinfo]
6. Para **Nome**, digite **MONGOLAB_URI**.
7. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
8. Selecione **Personalizado** na caixa suspensa Tipo (em vez do padrão **SQLAzure**).
9. Execute `npm install mongoose` para obter Mongoose, um driver para nós do MongoDB.
10. Configure um gancho em seu código para obter o URI de conexão do MongoLab de uma variável de ambiente e conecte:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Observação: o Azure adiciona o prefixo **CUSTOMCONNSTR_** à cadeia de conexão declarada originalmente e é por isso que o código faz referência a **CUSTOMCONNSTR_MONGOLAB_URI.** em vez de a **MONGOLAB_URI**.

Agora, vamos para o tutorial completo...

<a name="provision"></a>
## Provisionar o banco de dados

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Crie o aplicativo

Nesta seção você vai configurar seu ambiente de desenvolvimento e definir o código para aplicativo Web básico usando Node.js, Express e MongoDB. [Express] fornece uma estrutura de Modelo-Exibição-Controlador para nós, enquanto [Mongoose] é um driver para a comunicação com o MongoDB.

### Configuração

#### Gerar scaffolding e instalar módulos

1. Na linha de comando, crie e navegue até o diretório **tasklist**. Esse será o diretório do projeto.
2. Insira o seguinte comando para instalar o express.

		npm install express -g

	`-g` indica o modo global, que usamos para disponibilizar o módulo <strong>express</strong> sem especificar um caminho de diretório. Se você receber <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, use <strong>sudo</strong> para executar o npm em um nível de privilégio mais alto.

    A saída desse comando deve ser semelhante ao seguinte:

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)

3. Para criar o scaffolding que será usado para esse aplicativo, use o comando **express**:

    	express

    Observe que este tutorial está usando o Express v 4.x.x. Se você já tiver o gerador de aplicativo Express 3 instalado no seu sistema, você deve desinstalá-lo:

    	npm uninstall -g express

    Instale agora o gerador de novo para a versão 4:

    	npm install -g express-generator

	Assim que o comando **express** for executado, a saída deverá ser semelhante à seguinte:

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public
		create : ./public/javascripts
		create : ./public/images
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./routes
		create : ./routes/index.js
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	Após a conclusão do comando, você deve ter vários novos diretórios e arquivos no diretório **tasklist**.

4. Digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída desse comando deve ser semelhante ao seguinte:

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, quando você implantar esse aplicativo nos Aplicativos Web do Serviço de Aplicativo do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo.

5. Em seguida, digite o seguinte comando para instalar o módulo Mongoose localmente, bem como para salvar uma entrada para ele no arquivo **package.json**:

		npm install mongoose --save

	A saída desse comando deve ser semelhante ao seguinte:

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)

### O código

Agora que nosso ambiente e scaffolding estão prontos, ampliaremos o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também vai modificar o **app.js** existente e criar um novo arquivo de controlador **tasklist.js** para usar o modelo.

#### Criar o modelo

1. No diretório **tasklist**, crie um novo diretório com nome **models**.

2. No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3. Adicione o seguinte código ao arquivo **task.js**:

        var mongoose = require('mongoose'),
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String,
	      itemCategory  : String,
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Salve e feche o arquivo **task.js**.

#### Criar o controlador

1. No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2. Adicione os seguintes códigos ao **tasklist.js**. Isso carrega o módulo mongoose e o modelo da tarefa definido em **task.js**. A função TaskList é usada para criar a conexão com o servidor MongoDB com base no valor de **connection**, e fornece os métodos **showTasks**, **addTask** e **completeTasks**:

		var mongoose = require('mongoose'),
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
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

#### Modificar a exibição de índice

1. Altere os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

2. Substitua o conteúdo do arquivo **index.jade** pelo código abaixo. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.

		h1 #{title}
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
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Salve e feche o arquivo **index.jade**.

#### Substituir o app.js

1. No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.
2. Adicione o seguinte código ao início do arquivo **app.js**. Isso inicializará o **TaskList** com a cadeia de conexão para o servidor do MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Observe a segunda linha; você acessa uma variável de ambiente que você vai configurar mais tarde, que contém as informações de conexão para a instância mongo. Se você tiver uma instância local do Mongo em execução para fins de desenvolvimento, é aconselhável definir temporariamente esse valor como "localhost" em vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Localize estas linhas:

		app.use('/', routes);
		app.use('/users', users);

	E substitua-as por:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Isso adiciona as funções definidas no **tasklist.js** como rotas.

4. Para inicializar o seu aplicativo, vá até o final do seu arquivo **app.js** e adicione a seguinte linha:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Salve o arquivo **app.js**.

<a name="deploy"></a>
## Implantar o aplicativo

Agora que o aplicativo foi desenvolvido, é hora de criar um aplicativo Web no Serviço de Aplicativo do Azure para hospedá-lo, configurar esse aplicativo Web e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu aplicativo Web com esse URI para manter o URI separado do seu código. Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

As etapas desta seção usam a Interface de Linha de Comando do Azure para Mac, Linux e Windows para criar um novo aplicativo Web no Serviço de Aplicativo do Azure e, em seguida, usam o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

### Instalar a CLI do Azure

Para instalar a CLI do Azure, use o seguinte comando:

	npm install azure-cli -g

Se você já tiver instalado o <strong>SDK do Azure para o Node.js</strong> no <a href="/develop/nodejs/">Centro de Desenvolvedores do Azure</a>, então a CLI do Azure já deverá estar instalada. Para obter mais informações, consulte <a href="../virtual-machines-command-line-tools.md">CLI do Azure</a>.

Embora as ferramentas CLI do Azure tenham sido criadas principalmente para usuários do Mac e do Linux, elas se baseiam em Node.js e devem funcionar em qualquer sistema capaz de executar nós.

### Importar configurações de publicação

Antes de usar a CLI do Azure, você deve primeiro baixar um arquivo que contém informações sobre sua assinatura. Execute as seguintes etapas para baixar e importar este arquivo.

1. Na linha de comando, digite o seguinte comando para iniciar o navegador e vá até a página de download. Se solicitado, faça o login usando a conta associada à sua assinatura.

		azure account download

	![A página de download][download-publishing-settings]

	O download do arquivo deve começar automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo.

2. Após o download do arquivo, use o seguinte comando para importar as configurações:

		azure account import <path-to-file>

	Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior. Quando o comando for concluído, você verá uma saída semelhante à seguinte:

		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. Quando a importação for concluída, você deve excluir o arquivo de configurações de publicação, já que ele não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

### Criar um novo aplicativo Web e enviar o seu código por push

É muito fácil criar um aplicativo Web no Serviço de Aplicativo do Azure. Se esse for o seu primeiro aplicativo Web do Azure, você deve usar o portal. Se você já tiver pelo menos um, vá para a etapa 7.

1. No portal do Azure, clique em **Novo**. ![Novo][button-new]
2. Selecione **Computação > Aplicativo Web > Criação Rápida**.
<!-- ![Create Web App][screen-mongolab-newwebsite] -->
3. Digite um prefixo de URL. Escolha um nome de sua preferência, mas lembre-se de que ele deve ser exclusivo ('mymongoapp' provavelmente não estará disponível).
4. Clique em **Criar aplicativo Web**.
5. Quando a criação do aplicativo Web for concluída, clique no nome do aplicativo Web na lista de aplicativos Web. O painel Aplicativo Web é exibido.
<!-- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. Clique em **Configurar a implantação do controle do código-fonte** em **visão rápida**, selecione GitHub e insira seu nome de usuário git desejado e senha. Você usará essa senha ao enviar por push para o seu aplicativo Web (na etapa 9).  
7. Se você tiver criado o seu aplicativo Web usando as etapas acima, o comando a seguir pode concluir o processo. No entanto, se você já tiver mais de um aplicativo Web, ignore as etapas acima e crie um novo aplicativo Web usando esse mesmo comando. De seu diretório de projeto **tasklist**:

		azure site create myuniquewebappname --git  
	Substitua 'myuniquewebappname' pelo nome exclusivo para seu aplicativo Web. Se o aplicativo Web for criado como parte desse comando, você será direcionado ao datacenter no qual o aplicativo Web estará localizado. Selecione o datacenter geograficamente próximo ao seu banco de dados MongoLab.

	O parâmetro `--git` criará: * um repositório git local na pasta **tasklist**, se não houver nenhum. * um [Git remoto] chamado 'azure', que será usado para publicar o aplicativo no Azure. * um arquivo [iisnode. yml] que contém as configurações usadas pelo Azure para aplicativos de nó do host. * um arquivo .gitignore para evitar que a pasta de módulos do nó seja publicada para o .git.

	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha que começa com o **Aplicativo Web criado em** contém a URL para o aplicativo Web.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

9. Envie seu código:

		git push azure master  

	Ao enviar as alterações mais recentes do repositório Git ao aplicativo Web do Serviço de Aplicativo do Azure, você deve especificar que a ramificação de destino é **master**, pois isso é usada para o conteúdo do aplicativo Web. Se for solicitada uma senha, digite a senha que você criou quando configurou o Git de publicação para o aplicativo Web acima.

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

Já está quase pronto!

### Configure seu ambiente
Lembra-se do process.env.CUSTOMCONNSTR_MONGOLAB_URI no código? Queremos preencher essa variável de ambiente com o valor fornecido para o Azure durante o provisionamento do banco de dados MongoLab.

#### Obter a cadeia de conexão do MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### Adicionar a cadeia de conexão às variáveis de ambiente do aplicativo Web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## Sucesso!

Execute `azure site browse` do diretório do projeto para abrir automaticamente um navegador, ou abra um navegador e navegue manualmente para a URL do aplicativo Web (myuniquewebappname.azurewebsites.net):

![Uma página da Web que exibe uma lista de tarefas vazia][node-mongo-finished]

<a name="manage"></a>
## Gerenciar o banco de dados

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Parabéns! Você acabou de lançar um aplicativo Node. js apoiado por um banco de dados MongoDB hospedado em MongoLab! Agora que o banco de dados MongoLab foi criado, você poderá entrar em contato com [support@mongolab.com](mailto:support@mongolab.com)com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda para o MongoDB ou para o próprio driver do nó. Boa sorte!

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode. yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!---HONumber=July15_HO3-->