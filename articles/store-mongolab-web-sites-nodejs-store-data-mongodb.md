<properties 
	pageTitle="Site do Node. js com o MongoDB em MongoLab - Azure" 
	description="Saiba como criar um site Node.js do Azure que se conecta a uma instância do MongoDB hospedada no MongoLab." 
	services="web-sites, virtual-machines" 
	documentationCenter="nodejs" 
	authors="chrischang12" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/04/2014" 
	ms.author="mwasson"/>






# Criar um aplicativo do Node.js no Azure com MongoDB usando o suplemento MongoLab

<p><em>Por Eric Sedor, MongoLab</em></p>

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1. [Provisionar o banco de dados][provisionar] - o suplemento [MongoLab](http://mongolab.com) da Azure Store fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
2. [Criar o aplicativo][criar] - esse será um aplicativo simples do Node. js para manter uma lista de tarefas.
3. [Implantar o aplicativo][implantar] - com a reunião de alguns ganchos de configuração, a implantação do nosso código será moleza.
4. [Gerenciar o banco de dados][gerenciar] - finalmente, mostraremos o portal de gerenciamento do banco de dados baseado na web do MongoLab, onde você poderá pesquisar, visualizar e modificar dados facilmente.

A qualquer momento durante este tutorial, fique à vontade para enviar um email para [support@mongolab.com](mailto:support@mongolab.com) em caso de dúvidas.

Antes de continuar, certifique-se de que você tem o seguinte instalado:

* [Node.js] version 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Início rápido
Se você tiver alguma familiaridade com o Azure Store, use esta seção para fazer um início rápido. Caso contrário, vá para [Provisionar o banco de dados][provisionar] abaixo.
 
1. Abra a Azure Store.  
![Store][button-store]
2. Clique no complemento MongoLab.  
![MongoLab][entry-mongolab]
3. Clique no complemento MongoLab na lista de complementos e, em seguida, clique em **Informações de conexão**.  
![ConnectionInfoButton][button-connectioninfo]  
4. Copie o MONGOLAB_URI para a sua área de transferência.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Esse URI contém o nome de usuário do seu banco de dados e senha.  Trate-as como informações confidenciais e não as compartilhe.**
5. Adicione o valor à lista Cadeias de Conexão no menu de Configuração do seu aplicativo Web do Azure:  
![WebSiteConnectionStrings][focus-website-connectinfo]
6. Para **Nome**, digite MONGOLAB_URI.
7. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
8. Selecione **Personalizado** na caixa suspensa Tipo (em vez do padrão **SQLAzure**).
9. Execute `npm install mongoose` para obter Mongoose, um driver para nós do MongoDB.
10. Configure um gancho em seu código para obter o URI de conexão do MongoLab de uma variável de ambiente e conecte:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Observação: O Azure adiciona o prefixo **CUSTOMCONNSTR\_** à cadeia de conexão declarada originalmente e é por isso que o código referências é **CUSTOMCONNSTR\_MONGOLAB\_URI.** em vez de **MONGOLAB\_URI**.

Agora, vamos para o tutorial completo...

<h2><a name="provision"></a>Provisionar o banco de dados</h2>

[AZURE.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Criar o aplicativo</h2>

Nesta seção você vai configurar seu ambiente de desenvolvimento e definir o código para aplicativo Web básico usando Node.js, Express e MongoDB. [O Express] fornece uma estrutura de Modelo-Exibição-Controlador para nós, enquanto o [Mongoose] é um driver para a comunicação com o MongoDB no nó.

### Configuração

#### Gerar scaffolding e instalar módulos

1. Na linha de comando, crie e navegue até o diretório **tasklist**. Esse será o diretório do projeto.
2. Insira o seguinte comando para instalar o express.

		npm install express -g
 
	`-g` indica o modo global, que usamos para disponibilizar o módulo <strong>express</strong> sem especificar um caminho de diretório. Se você receber<strong>Error: EPERM, chmod '/usr/local/bin/express'</strong> use <strong>sudo</strong> para executar o npm em um nível de privilégio mais elevado.

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

    Now install the new generator for version 4.x.x:

    	npm install -g express-generator

	Uma vez que o comando **express** for executado, a saída deve ser semelhante a seguinte:

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

	O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, ao implantar esse aplicativo em um site do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo.

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

Agora que nosso o ambiente e o scaffolding estão prontos, ampliaremos o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também vai modificar o **app.js** existente e criar um novo arquivo de controlador **tasklist.js** para usar o modelo.

#### Criar o modelo

1. No diretório **tasklist**, crie um novo diretório chamado **models**.

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

2. Adicione os seguintes códigos ao **tasklist.js**. Isso carrega o módulo mongoose e o modelo da tarefa definido em **task.js**. A função TaskList é usada para criar a conexão com o servidor MongoDB com base no valor da **connection** e fornece os métodos **showTasks**, **addTask** e **completeTasks**:

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

1. Mude os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

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
2. Adicione o seguinte código no início do arquivo **app.js**. Isso inicializará o **TaskList** com a cadeia de conexão para o servidor do MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Observe a segunda linha. Você acessa uma variável de ambiente que vai configurar mais tarde, que contém as informações de conexão para a instância mongo. Se você tiver uma instância local do mongo em execução para fins de desenvolvimento, é aconselhável definir temporariamente esse valor como "localhost" em vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Localize estas linhas:
		
		app.use('/', routes);
		app.use('/users', users);

	E substitua-as por:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Isso adiciona as funções definidas no **tasklist.js** como rotas.

4. Para inicializar o aplicativo, vá até o final do seu arquivo **js** e adicione a seguinte linha:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Salve o arquivo **app.js**.

<h2><a name="deploy"></a>Implantar o aplicativo</h2>

Agora que o aplicativo foi desenvolvido, é hora de criar um Site do Azure para hospedá-lo, configurar esse site e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu site com esse URI para manter o URI separado do seu código.  Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo site do Azure e, em seguida, usa o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

### Instalar as ferramentas de linha de comando do Azure para Mac e Linux

Para instalar as ferramentas de linha de comando, use o seguinte comando:
	
	npm install azure-cli -g

Se você já tiver instalado o <strong>SDK do Azure para o Node.js</strong> do <a href="/develop/nodejs/">Centro de Desenvolvedores do Azure</a>, então as ferramentas de linha de comando já deverão estar instaladas. Para obter mais informações, consulte <a href="virtual-machines-command-line-tools.md">Ferramentas de linha de comando do Azure para Mac e Linux</a>.

Enquanto as ferramentas de linha de comando do Azure foram criadas principalmente para usuários do Mac e do Linux, eles se baseiam em Node. js e devem funcionar em qualquer sistema capaz de executar nós.

### Importar configurações de publicação

Antes de usar as ferramentas de linha de comando com o Azure, você deve primeiro baixar um arquivo que contém informações sobre sua assinatura. Execute as seguintes etapas para baixar e importar este arquivo.

1. Na linha de comando, digite o seguinte comando para iniciar o navegador e vá até a página de download. Se solicitado, faça o logon usando a conta associada à sua assinatura.

		azure account download
	
	![The download page][download-publishing-settings]
	
	O download do arquivo deve começar automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo.

2. Após o download do arquivo, use o seguinte comando para importar as configurações:

		azure account import <path-to-file>
		
	Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior. Quando o comando for concluído, você verá uma saída semelhante à seguinte:
	
		info:   Executando comando account import
		info:   Assinatura encontrada: subscriptionname
		info:   Definindo assinatura padrão como: subscriptionname
		warn:   O arquivo '/Users/mongolab/.azure/publishSettings.xml' contém informações confidenciais.
		warn:   lembre-se de excluí-lo agora que foi importado.
		info:   Configurações de publicação da conta importadas com sucesso
		info:   comando account import OK


3. Quando a importação for concluída, você deve excluir o arquivo de configurações de publicação, já que ele não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

### Criar um novo site e seu código de envio

Criar um site no Azure é muito fácil. Se esse for o seu primeiro site do Azure, você deve usar o portal. Se você já tiver pelo menos um, vá para a etapa 7.

1. No portal do Azure, clique em **Novo**.    
![New][button-new]
2. Escolha **Computação > Site > Criação Rápida**. 
![CreateSite][screen-mongolab-newwebsite]
3. Digite um prefixo de URL. Escolha um nome de sua preferência, mas lembre-se de que ele deve ser exclusivo ('mymongoapp' provavelmente não estará disponível).
4. Clique em **Criar Site**.
5. Quando a criação do site for concluída, clique no nome do site na lista de sites. O painel do site é exibido.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
6. Clique em **Configurar a implantação do controle de origem** em **visão rápida**, escolha GitHub e insira seu nome de usuário git desejado e senha. Você usará essa senha ao enviar para seu site (na etapa 9).  
7. Se você tiver criado seu site usando as etapas acima, o comando a seguir pode concluir o processo. No entanto, se você tiver mais de um site do Azure, ignore as etapas acima e crie um novo site usando o mesmo comando. De seu diretório de projeto **tasklist**: 

		azure site create myuniquesitename --git  
	Substitua 'myuniquesitename' pelo nome de site exclusivo para seu site. Se o site for criado como parte do comando, você será solicitado pelo datacenter onde o site está localizado. Selecione o data center geograficamente mais perto de seu banco de dados do MongoLab.
	
	O parâmetro `--git`' criará:
	* um repositório local de Git na pasta **tasklist**, se não houver nenhum.
	* um [Git remoto] chamado 'azure', que será usado para publicar o aplicativo no Azure.
	* um arquivo [iisnode. yml], que contém as configurações usadas pelo Azure para hospedar aplicativos de nó.
	* um arquivo .gitignore para evitar que a pasta de módulos do nó seja publicada em .git.  
	  
	Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha começando com **Site criado em** contém o URL para o site.

		info:   Executando o comando site create
		info:   Usando o local southcentraluswebspace
		info:   Executando `git init`
		info:   Criando arquivo web.config padrão
		info:   Criando um novo site
		info:   Site criado em mongodbtasklist.azurewebsites.net
		info:   Inicializando o repositório
		info:   Repositório inicializado
		info:   Executando `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

		git add .
		git commit -m "adding files"

9. Envie seu código:

		git push azure master  
	Ao enviar as mudanças do repositório Git mais recentes para o Site do Azure, você deve especificar que a ramificação de destino é **master**, já que ela é usada para o conteúdo do site. Se for solicitada uma senha, digite a senha que você criou quando configurou o Git de publicação para o site acima.
	
	Você verá saídas semelhantes ao seguinte. Durante a implantação, o Azure baixará todos os módulos npm. 

		Contando objetos: 17, concluído.
		Compactação delta com até oito threads.
		Compactar objetos: 100% (13/13), concluído.
		Gravar objetos: 100% (17/17), KiB 3.21, concluído.
		Total 17 (delta 0), 0 reutilizado (delta 0)
		remoto: Nova implantação recebida.
		remoto: Atualizando a ramificação 'master'.
		remoto: Preparando a implantação para a id de confirmação 'ef276f3042'.
		remoto: Preparando arquivos para implantação.
		remoto: NPM em execução.
		...
		remoto: Implantação de Web.config para habilitar a ativação do Node.js.
		remoto: Implantação bem-sucedida.
		Para https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [nova ramificação]      mestre -> mestre
 
Já está quase pronto!

### Configure seu ambiente
Lembrou-se de process.env.CUSTOMCONNSTR\_MONGOLAB\_URI no código? Queremos preencher essa variável de ambiente com o valor fornecido para o Azure durante o provisionamento do banco de dados MongoLab.

#### Obter a cadeia de conexão do MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Adicionar a cadeia de conexão às variáveis de ambiente do site

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Sucesso!

Execute `azure site browse` do diretório do projeto para abrir automaticamente um navegador, ou abra um navegador e navegue manualmente para o URL do site (myuniquesite.azurewebsites.net):

![A webpage displaying an empty tasklist][node-mongo-finished]

<h2><a name="manage"></a>Gerenciar o banco de dados</h2>

[AZURE.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Parabéns! Você acabou de lançar um aplicativo Node. js apoiado por um banco de dados MongoDB hospedado em MongoLab! Agora que você tem um banco de dados MongoLab, você pode entrar em contato com [support@mongolab.com] com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda com o MongoDB ou o próprio driver de nó. Boa sorte!



[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png 
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provisionar]: #provision
[criar]: #create
[implanatar]: #deploy
[gerenciar]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[gratuitamente]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[Azure sdk para nó]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Ferramenta de linha de comando do Azure para Mac e Linux]: virtual-machines-command-line-tools.md
[Centro de desenvolvedores do Azure]: /develop/nodejs/
[Criar e implantar um aplicativo Node.js em Sites do Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Publicando Sites do Azure com o Git]: /develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Aplicativo Web do Node.js com Armazenamento no MongoDB (Máquina Virtual)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




<!--HONumber=49-->