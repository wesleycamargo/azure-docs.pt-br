<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Site com MongoDB" pageTitle="Site Node. js com o MongoDB em MongoLab - Azure" metaKeywords="" description="Aprenda a criar um Site Node. js do Azure que se conecta a uma instância do MongoDB hospedada no MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Criar um aplicativo do Node. js no Azure com o MongoDB usando o complemento do MongoLab" authors=""  solutions="" writer="" manager="" editor=""  />





# Criar um aplicativo do Node.js no Azure com MongoDB usando o suplemento MongoLab

<p><em>Por Eric Sedor, MongoLab</em></p>

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1. [Provisionar o banco de dados][provision] - O complemento do Azure Store [MongoLab](http://mongolab.com) fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
1. [Criar o aplicativo][create] - Esse será um aplicativo simples do Node. js para manter uma lista de tarefas.
1. [Implantar o aplicativo][deploy] - Ao reunir alguns ganchos de configuração, a implantação de nosso código será moleza.
1. [Gerenciar o banco de dados][manage] - Por fim, mostraremos o portal de gerenciamento de banco de dados baseado na web do MongoLab, onde você pode pesquisar, visualizar e modificar os dados com facilidade.

A qualquer momento durante este tutorial, fique à vontade para mandar um e-mail para [support@mongolab.com](mailto:support@mongolab.com) em caso de dúvidas.

Antes de continuar, certifique-se de que você tem o seguinte instalado:

* [Node.js] versão 0.8.14+

* [Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Início rápido
Se você tiver alguma familiaridade com o Azure Store, use esta seção para fazer um início rápido. Caso contrário, vá para [Provisionar o banco de dados][provision] abaixo.
 
1. Abra o Azure Store.  
![Store][button-store]
1. Clique no complemento MongoLab.  
![MongoLab][entry-mongolab]
1. Clique no complemento MongoLab na lista de complementos e, em seguida, clique em **Informações de conexão**.  
![ConnectionInfoButton][button-connectioninfo]  
1. Copie o MONGOLAB_URI para a área de transferência.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Esse URI contém seu nome de usuário do banco de dados e senha.  Tratá-lo como informações confidenciais e não a compartilhe.**
1. Adicione valor à lista de cadeias de conexão no menu de configuração de seu aplicativo da Web do Azure:  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. Para **Nome**, digite MONGOLAB\_URI.
1. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
1. Selecione **personalizado** na lista suspensa Tipo (em vez do padrão **SQLAzure**).
1. Execute `npm install mongoose` para obter Mongoose, um driver do nó do MongoDB.
1. Configure um gancho em seu código para obter o URI de conexão do MongoLab de uma variável de ambiente e conecte:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Observação: o Azure adiciona o prefixo **CUSTOMCONNSTR\_** à cadeia de conexão declarada originalmente, e é por isso que o código faz referência ao **CUSTOMCONNSTR\_MONGOLAB\_URI.** em vez de **MONGOLAB\_URI**.

Agora, vamos para o tutorial completo...

<h2><a name="provision"></a>Provisionar o banco de dados</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Crie o aplicativo</h2>

Nesta seção você vai configurar seu ambiente de desenvolvimento e definir o código para aplicativo Web básico de lista de tarefa usando Node.js, Express e MongoDB. [Express] fornece uma estrutura de controlador de exibição para o nó, enquanto que o [Mongoose] é um driver para comunicar-se com MongoDB no nó.

### Instalação

#### Gerar scaffolding e instalar módulos

1. Na linha de comando, crie e navegue até o diretório **tasklist**. Esse será o diretório do projeto.
2. Insira o seguinte comando para instalar o express.

		npm install express -g
 
	`-g` indica o modo global, que usamos para disponibilizar o módulo <strong>express</strong> sem especificar um caminho de diretório. Se você receber <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, use <strong>sudo</strong> para executar npm em um nível de privilégio mais elevado.

    A saída deste comando deve aparecer, semelhante ao seguinte:

		express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)
 
3. Para criar o scaffolding que será usado para esse aplicativo, use o comando **express**:

    express

	A saída deste comando deve aparecer, semelhante ao seguinte:

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
   		create : ./views
   		create : ./views/layout.jade
   		create : ./views/index.jade
		
   		dont forget to install dependencies:
   		$ cd . && npm install

	Após a conclusão do comando, você deve ter vários novos diretórios e arquivos no diretório **tasklist**.
	
4. Digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída deste comando deve aparecer, semelhante ao seguinte:

		express@3.3.4 node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

		jade@0.33.0 node_modules\jade
		├── character-parser@1.0.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── with@1.1.0 (uglify-js@2.3.6)
		├── constantinople@1.0.1 (uglify-js@2.3.6)
		├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── monocle@0.1.48 (readdirp@0.2.5)

	O arquivo **package.json** está em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Depois, quando você implantar esse aplicativo em um Site do Azure, esse arquivo será usado para determinar quais módulos devem ser instalados no Azure para oferecer suporte ao seu aplicativo.

5. Em seguida, digite o seguinte comando para instalar o módulo Mongoose localmente, bem como para salvar uma entrada para ele no arquivo **package.json**:

		npm install mongoose --save

	A saída deste comando deve aparecer, semelhante ao seguinte:

		mongoose@3.6.15 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.3
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── ms@0.1.0
		├── mpromise@0.2.1 (sliced@0.0.4)
		└── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    Você pode ignorar com segurança quaisquer mensagens sobre a instalação do analisador de bson C++.
	
### O código

Agora que nosso ambiente e scaffolding estão prontos, ampliaremos o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também vai modificar o **app.js** existente e criar um novo arquivo de controlador **tasklist.js** para utilizar o modelo.

#### Criar o modelo

1. No diretório **tasklist**, crie um novo diretório com nome **models**.

2. No diretório **models**, crie um novo arquivo com nome **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3. Adicione o seguinte código ao arquivo **task.js**:

        var mongoose = require('mongoose')
	      , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	        itemName      : String
	      , itemCategory  : String
	      , itemCompleted : { type: Boolean, default: false }
	      , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Salve e feche o arquivo **task.js**.

#### Criar o controlador

1. No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2. Adicione os códigos ao **tasklist.js**. Isso carrega o módulo mongoose e o modelo de tarefa definido em **task.js**. A função TaskList é usada para criar a conexão com o servidor MongoDB com base no valor da **conexão** e, em seguida, fornece os métodos **showTasks**, **addTask** e **completeTasks**:

		var mongoose = require('mongoose')
	      , task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

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
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. Salve e feche o arquivo **index.jade**.

#### Substituir o app.js

1. No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.
1. Adicione o seguinte código ao início do arquivo **app.js**. Isso irá inicializar **TaskList** com a cadeia de conexão para o servidor do MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Observe a segunda linha; você acessa uma variável de ambiente que você vai configurar mais tarde, que contém as informações de conexão para a instância mongo. Se você tiver uma instância local do mongo em execução para fins de desenvolvimento, é aconselhável definir temporariamente esse valor para "localhost" em vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

2. Localize as linhas que começam com `app.get` e substitua pelas seguintes linhas:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Isso adiciona as funções definidas no **tasklist.js** como rotas.

4. Salve o arquivo **app.js**.

<h2><a name="deploy"></a>Implantar o aplicativo</h2>

Agora que o aplicativo foi desenvolvido, é hora de criar um Site do Azure para hospedá-lo, configurar esse site e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu site com esse URI para manter o URI separado do seu código.  Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo Site do Azure e, em seguida, usa o Git para implantar seu aplicativo. Para executar essas etapas, você deve ter uma assinatura do Azure.

### Instalar as ferramentas de linha de comando do Azure para Mac e Linux

Para instalar as ferramentas de linha de comando, use o seguinte comando:
	
	npm install azure-cli -g

Se você já tiver instalado o <strong>SDK do Azure para o Node. js</strong> do <a href="/pt-br/develop/nodejs/">Centro de Desenvolvedores do Azure</a>, então as ferramentas de linha de comando já devem estar instaladas. Para obter mais informações, consulte <a href="/pt-br/develop/nodejs/how-to-guides/command-line-tools/">Ferramentas de linha de comando do Azure para Mac e Linux</a>.

Enquanto as ferramentas de linha de comando do Azure foram criadas principalmente para usuários do Mac e do Linux, eles se baseiam em Node. js e devem funcionar em qualquer sistema capaz de executar nós.

### Importar configurações de publicação

Antes de usar as ferramentas de linha de comando com o Azure, você deve primeiro baixar um arquivo contendo informações sobre sua assinatura. Execute as seguintes etapas para fazer o download e importar este arquivo.

1. Na linha de comando, digite o seguinte comando para iniciar o navegador e vá até a página de download. Se solicitado, faça o login usando a conta associada à sua assinatura.

		azure account download
	
	![A página de download][download-publishing-settings]
	
	O download do arquivo deve começar automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo.

3. Após o download do arquivo, use o seguinte comando para importar as configurações:

		azure account import <path-to-file>
		
	Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior. Quando o comando for concluído, você verá uma saída semelhante à seguinte:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


4. Quando a importação for concluída, você deve excluir o arquivo de configurações de publicação que não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

### Criar um novo site e seu código de envio

Criar um site no Azure é muito fácil. Se esse for o seu primeiro site do Azure, você deve usar o portal. Se você já tiver pelo menos um, vá para a etapa 7.

1. No Portal do Azure, clique em **Novo**.    
![Novo][button-new]
1. Selecione **Computação > Site > Criação Rápida**. 
![CreateSite][screen-mongolab-newwebsite]
1. Digite um prefixo de URL. Escolha um nome , mas tenha em mente que isso deve ser exclusivo ('mymongoapp' provavelmente não estará disponível).
1. Clique em **Criar Site**.
1. Quando a criação do site for concluída, clique no nome do site na lista do site. O painel do site é exibido.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. Clique em **Configurar a publicação do Git** em **visão rápida**e insira seu nome de usuário Git e senha desejados. Você usará essa senha ao enviar para seu site (na etapa 9).  
1. Se você tiver criado seu site usando as etapas acima, o comando a seguir pode concluir o processo. No entanto, se você tiver mais de um site do Azure, ignore as etapas acima e crie um novo site usando o mesmo comando. De seu diretório de projeto **tasklist**: 

		azure site create myuniquesitename --git  
	Substitua 'myuniquesitename' pelo nome de site exclusivo para seu site. Se o site for criado como parte do comando, você será solicitado pelo datacenter onde o site está localizado. Selecione o data center geograficamente mais perto de seu banco de dados MongoLab.
	
	O parâmetro `--git` será criado:
	R. um repositório local de Git na pasta **tasklist**, se não houver nenhum.
	R. um [Git remoto] chamado 'azure', que será usado para publicar o aplicativo no Azure.
	R. um arquivo [iisnode.yml], que contém as configurações usadas pelo Azure para hospedar aplicativos de nó.
	R. um arquivo .gitignore para evitar que a pasta de módulos do nó seja publicada em .git.  
	  
	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha começando com **site criado em** contém o URL para o site.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

1. Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

1. Envie seu código:

		git push azure master  
	Ao enviar as alterações do repositório de Git mais recentes para o Site do Azure, você deve especificar que a ramificação de destino é **mestre** já que é usado para o conteúdo do site. Se for solicitada uma senha, digite a senha que você criou quando configurou o Git de publicação para o site acima.
	
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

### Configurar seu ambiente
Lembra do process.env.CUSTOMCONNSTR\_MONGOLAB\_URI no código? Queremos preencher essa variável de ambiente com o valor fornecido para o Azure durante o provisionamento do banco de dados MongoLab.

#### Obtenha a cadeia de conexão do MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Adicionar a cadeia de conexão às variáveis de ambiente do site

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Sucesso!

Execute `azure site browse` do diretório do projeto para abrir automaticamente um navegador, ou abra um navegador e navegue manualmente para o URL do site (myuniquesite.azurewebsites.net):

![Uma página da Web que exibe uma lista de tarefas vazia][node-mongo-finished]

<h2><a name="manage"></a>Gerenciar o banco de dados</h2>

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Parabéns! Você acabou de lançar um aplicativo Node. js apoiado por um banco de dados MongoDB hospedado em MongoLab! Agora que você tem um banco de dados MongoLab, você pode entrar em contato com [support@mongolab.com](mailto:support@mongolab.com) com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda com o MongoDB ou o próprio driver de nó. Boa sorte!



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
[grátis]: /pt-br/pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Ferramenta de linha de comando do Azure para Mac e Linux]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/
[Centro de Desenvolvedores do Azure (a página pode estar em inglês)]: /pt-br/develop/nodejs/
[Criar e implantar um aplicativo do Node.js no Site do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
[Publicando Sites do Azure com Git]: /pt-br/develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Aplicativo Web do Node. js com armazenamento no MongoDB (Máquina Virtual)]: /pt-br/develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




