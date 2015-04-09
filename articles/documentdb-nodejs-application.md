<properties 
	pageTitle="Compilar um aplicativo Web Node.js usando o Banco de Dados de Documentos | Azure" 
	description="Saiba como usar o Banco de Dados de Documentos do Microsoft Azure para armazenar e acessar dados de um aplicativo Web Express do Node.js hospedado nos Sites do Azure." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="03/20/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>Compilar um aplicativo Web do Node.js usando o Banco de Dados de Documentos

Este tutorial mostra a você como usar o serviço do Banco de Dados de Documentos do Azure para armazenar e acessar dados de um aplicativo Express do Node.js hospedado nos Sites do Azure.

É recomendável começar assistindo ao vídeo a seguir, em que Andrew Liu mostra como provisionar uma conta de banco de dados do Banco de Dados de Documentos do Azure e armazenar documentos JSON em seu aplicativo do Node.js. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Em seguida, volte a este artigo, onde você aprenderá as respostas para as seguintes perguntas:

- Como posso trabalhar com o Banco de Dados de Documentos usando o módulo nmp do banco de dados?
- Como posso implantar o aplicativo Web nos Sites do Azure?

Seguindo este tutorial, você criará um aplicativo simples baseado na Web
de gerenciamento de tarefas que permite criar, recuperar e
concluir tarefas. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos
do Azure.

![Screen shot of the My Todo List application created in this tutorial](./media/documentdb-nodejs-application/image1.png)

Não tem tempo para concluir o tutorial e quer apenas obter a solução completa no Github? Sem problemas, [aqui](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo) está.

## <a name="_Toc395783176"></a>Pré-requisitos

> [AZURE.TIP] Este tutorial presume que você tenha alguma experiência com o uso do Node.js e dos Sites do Azure.

Antes de seguir as instruções neste artigo, você deve verificar
se tem o seguinte:

- Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](../../pricing/free-trial/).
- [Node.js][] versão v0.10.29 ou superior.
- [Gerador do Express](http://www.expressjs.com/starter/generator.html) (você pode instalá-lo por meio de `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Etapa 1: Criar uma conta de banco de dados do Banco de Dados de Documentos

Vamos começar criando uma conta do Banco de Dados de Documentos. Se já tiver uma conta, você pode passar para a [Etapa 2: Criar um novo aplicativo Node.js](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Etapa 2: Criar um novo aplicativo Node.js

Agora, vamos criar um projeto básico do tipo Hello World no Node.js usando a estrutura [Express](http://expressjs.com/).

1. Abra seu terminal favorito.

2. Use o gerador do Express para gerar um novo aplicativo chamado **todo**.

		express todo

3. Abra o novo diretório **todo** e instale as dependências.

		cd todo
		npm install

4. Execute seu novo aplicativo.

		npm start

5. Você pode ver o novo aplicativo navegando em seu navegador até [http://localhost:3000](http://localhost:3000).

	![Screenshot of the Hello World application in a browser window](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Etapa 3: Instalar módulos adicionais

O arquivo **package.json** é um dos arquivos criados na raiz do
projeto. Esse arquivo contém uma lista de módulos adicionais que são
necessários para seu aplicativo do Node.js. Posteriormente, quando você implantar o
aplicativo para um site do Azure, esse arquivo será usado para determinar
quais módulos devem ser instalados no Azure para dar suporte a seu aplicativo. Ainda precisamos instalar mais dois pacotes para este tutorial.

1. No terminal, instale o módulo **async** via npm.

		npm install async --save

1. Instale o módulo **documentdb** via npm. É nesse módulo em que toda a mágica do Banco de Dados de Documentos acontece.

		npm install documentdb --save

3. Uma verificação rápida do arquivo **package.json** do aplicativo deve mostrar os módulos adicionais. Este arquivo informa ao Azure quais pacotes baixar e instalar quando seu aplicativo for executado. Ele deve se parecer com o exemplo a seguir.

	![Screenshot of the package.json tab](./media/documentdb-nodejs-application/image17.png)

       Ele informa ao Node (e mais tarde ao Azure) que seu aplicativo depende desses módulos adicionais.

## <a name="_Toc395783180"></a>Etapa 4: Usando o serviço do Banco de Dados de Documento em um aplicativo de nó

Isso cuida de toda a instalação e configuração inicial. Agora vamos ao motivo de estarmos aqui, ou seja, para gravar algum código usando o Banco de Dados de Documentos do Azure.

### Criar o modelo

1. No diretório do projeto, crie um novo diretório chamado **models**.
2. No diretório **models**, crie um novo arquivo chamado **taskDao.js**. O arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.
3. No mesmo diretório **models**, crie outro novo arquivo chamado **docdbUtils.js**. Esse arquivo conterá códigos úteis e reutilizáveis, que serão usados em todo o nosso aplicativo. 
4. Copie o código a seguir para **docdbUtils.js**

		var DocumentDBClient = require('documentdb').DocumentClient;
			
		var DocDBUtils = {
		    getOrCreateDatabase: function (client, databaseId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: databaseId
		            }]
		        };
		
		        client.queryDatabases(querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                if (results.length === 0) {
		                    var databaseSpec = {
		                        id: databaseId
		                    };
		
		                    client.createDatabase(databaseSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    },
		
		    getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: collectionId
		            }]
		        };
		        
		        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {		
		                if (results.length === 0) {
		                    var collectionSpec = {
		                        id: collectionId
		                    };
		
		                    client.createCollection(databaseLink, collectionSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    }
		};
				
		module.exports = DocDBUtils;
		
3. Salve e feche o arquivo **docdbUtils.js**.

4. No início do arquivo **taskDao.js**, adicione o seguinte código para fazer referência a **DocumentDBClient** e **docdbUtils.js**, criados anteriormente:

        var DocumentDBClient = require('documentdb').DocumentClient;
		var docdbUtils = require('./docdbUtils');

4. Em seguida, você adicionará o código para definir e exportar o objeto da tarefa. Ele é responsável por inicializar nosso objeto Task e por configurar o Banco de Dados e a Coleção de Documentos que nós usaremos.

		function TaskDao(documentDBClient, databaseId, collectionId) {
		  this.client = documentDBClient;
		  this.databaseId = databaseId;
		  this.collectionId = collectionId;
		
		  this.database = null;
		  this.collection = null;
		}
		
		module.exports = TaskDao;

5. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto da Tarefa, que permitem interações com os dados armazenados no Banco de Dados de Documentos.

		TaskDao.prototype = {
		    init: function (callback) {
		        var self = this;
		
		        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
		            if (err) {
		                callback(err);

		            } else {
		                self.database = db;
		                docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        self.collection = coll;
		                    }
		                });
		            }
		        });
		    },
		
		    find: function (querySpec, callback) {
		        var self = this;
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results);
		            }
		        });
		    },
		
		    addItem: function (item, callback) {
		        var self = this;
		
		        item.date = Date.now();
		        item.completed = false;
		
		        self.client.createDocument(self.collection._self, item, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, doc);
		            }
		        });
		    },
		
		    updateItem: function (itemId, callback) {
		        var self = this;
		
		        self.getItem(itemId, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                doc.completed = true;
		
		                self.client.replaceDocument(doc._self, doc, function (err, replaced) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        callback(null, replaced);
		                    }
		                });
		            }
		        });
		    },
		
		    getItem: function (itemId, callback) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: itemId
		            }]
		        };
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results[0]);
		            }
		        });
		    }
		};

6. Salve e feche o arquivo **taskDao.js**. 

### Criar o controlador

1. No diretório **routes** do seu projeto, crie um novo arquivo chamado **tasklist.js**. 
2. Adicione os seguintes códigos ao **tasklist.js**. Isso carrega os módulos DocumentDBClient e async, que são usados pelo **tasklist.js**. Isso também definiu a função de **TaskList**, que é transmitida a uma instância do objeto da **Tarefa** definido anteriormente:

		var DocumentDBClient = require('documentdb').DocumentClient;
		var async = require('async');
		
		function TaskList(taskDao) {
		  this.taskDao = taskDao;
		}
		
		module.exports = TaskList;

3. Continue adicionando ao arquivo **tasklist.js**, adicionando os métodos usados para **showTasks, addTask** e **completeTasks**:
		
		TaskList.prototype = {
		    showTasks: function (req, res) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.completed=@completed',
		            parameters: [{
		                name: '@completed',
		                value: false
		            }]
		        };
		
		        self.taskDao.find(querySpec, function (err, items) {
		            if (err) {
		                throw (err);
		            }
		
		            res.render('index', {
		                title: 'My ToDo List ',
		                tasks: items
		            });
		        });
		    },
		
		    addTask: function (req, res) {
		        var self = this;
		        var item = req.body;
		
		        self.taskDao.addItem(item, function (err) {
		            if (err) {
		                throw (err);
		            }
		
		            res.redirect('/');
		        });
		    },
		
		    completeTask: function (req, res) {
		        var self = this;
		        var completedTasks = Object.keys(req.body);
		
		        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		            self.taskDao.updateItem(completedTask, function (err) {
		                if (err) {
		                    callback(err);
		                } else {
		                    callback(null);
		                }
		            });
		        }, function goHome(err) {
		            if (err) {
		                throw err;
		            } else {
		                res.redirect('/');
		            }
		        });
		    }
		};


4. Salve e feche o arquivo **tasklist.js**.
 
### Adicionar config.json

1. No diretório do projeto, crie um novo arquivo chamado **config.js**.
2. Adicione o seguinte a **config.json**. Isso define as configurações e valores necessários para nosso aplicativo.

		var config = {}
		
		config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.databaseId = "ToDoList";
		config.collectionId = "Items";
		
		module.exports = config;

3. No arquivo **config.js**, atualize os valores de HOST e AUTH_KEY usando os valores encontrados na lâmina Chaves da sua conta do Banco de Dados de Documentos no [Portal de Visualização do Azure](http://portal.azure.com):

4. Salve e feche o arquivo **config.js**.
 
### Modificar app.js

1. No diretório do projeto, abra o arquivo **app.js**. Esse arquivo foi criado anteriormente, quando o aplicativo Web Express foi criado.
2. Adicione o seguinte código à parte superior de **app.js**
	
		var DocumentDBClient = require('documentdb').DocumentClient;
		var config = require('./config');
		var TaskList = require('./routes/tasklist');
		var TaskDao = require('./models/taskDao');

3. Esse código define o arquivo de configuração a ser usado e lê os valores desse arquivo para algumas variáveis que usaremos em breve.
4. Substitua as duas linhas a seguir no arquivo **app.js**:

		app.use('/', routes);
		app.use('/users', users); 

      pelo seguinte trecho:

		var docDbClient = new DocumentDBClient(config.host, {
		    masterKey: config.authKey
		});
		var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
		var taskList = new TaskList(taskDao);
		taskDao.init();
		
		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));


6. Essas linhas definem uma nova instância do nosso objeto **TaskDao**, com uma nova conexão ao Banco de Dados de Documentos (usando os valores lidos em **config.js**), inicializam o objeto da tarefa e associam ações de formulário a métodos em nosso controlador **TaskList**. 

7. Por fim, salve e feche o arquivo **app.js**. Nós estamos quase acabando.
 
## <a name="_Toc395783181"></a>Etapa 5: Compilar uma interface do usuário

Agora vamos voltar a atenção para criar a interface do usuário, desse modo, um usuário pode realmente interagir com nosso aplicativo. O aplicativo Express que criamos usa o **Jade** como mecanismo de exibição. Para saber mais sobre o Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar o [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante. 
2. Abra o arquivo **layout.jade** encontrado na pasta **views** e substitua o conteúdo pelo seguinte:
	
		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body
		    nav.navbar.navbar-inverse.navbar-fixed-top
		      div.navbar-header
		        a.navbar-brand(href='#') My Tasks
		    block content
		    script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
		    script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



	Isso instrui o mecanismo **Jade** a renderizar um HTML para nosso aplicativo e cria um **bloco** chamado **content**, onde podemos fornecer o layout para nossas páginas de conteúdo.
	Salve e feche o arquivo **layout.jade**.

4. Agora, abra o arquivo **index.jade**, a exibição que será usada pelo nosso aplicativo, e substitua o conteúdo do arquivo pelo seguinte:

		extends layout
		
		block content
		  h1 #{title}
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
		            td #{task.name}
		            td #{task.category}
		            - var date  = new Date(task.date);
		            - var day   = date.getDate();
		            - var month = date.getMonth() + 1;
		            - var year  = date.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="name", type="textbox")
		    label Item Category:
		    input(name="category", type="textbox")
		    br
		    button.btn(type="submit") Add item

	Isso estende o layout e fornece conteúdo para o espaço reservado **content** que vimos no arquivo **layout.jade** anteriormente.
	
	Nesse layout criamos dois formulários HTML. 
	O primeiro formulário contém uma tabela para nossos dados e um botão que nos permite atualizar itens postando no método **/completetask** do nosso controlador.
	O segundo formulário contém dois campos de entrada e um botão que permite criar um novo item postando no método **/addtask** do controlador.
	
	Isso deve ser tudo o que precisamos para que nosso aplicativo funcione.

5. Abra o arquivo **style.css** no diretório **public\stylesheets** e substitua o código pelo seguinte:

		body {
		  padding: 50px;
		  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
		}
		a {
		  color: #00B7FF;
		}
		.well label {
		  display: block;
		}
		.well input {
		  margin-bottom: 5px;
		}
		.btn {
		  margin-top: 5px;
		  border: outset 1px #C8C8C8;
		}

	Salve e feche o arquivo **style.css**.

## <a name="_Toc395783181"></a>Etapa 6: Execute seu aplicativo localmente

1. Para testar o aplicativo no computador local, execute `npm start` em um terminal para iniciar o aplicativo e iniciar um navegador com uma página semelhante à imagem a seguir:

	![Screenshot of the MyTodo List application in a browser window](./media/documentdb-nodejs-application/image18.png)


2. Use os campos fornecidos para o Item, o nome do Item e a categoria para inserir
informações e clique em **Adicionar Item**.

3. A página deve ser atualizada para exibir o item recém-criado na lista
ToDo.

	![Screenshot of the application with a new item in the ToDo list](./media/documentdb-nodejs-application/image19.png)

4. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir
e, em seguida, clicar em **Atualizar tarefas**.

## <a name="_Toc395783182"></a>Etapa 7: Implantar o aplicativo no Azure

1. Se ainda não tiver feito isso, habilite um repositório git para seu Site do Azure. Você pode encontrar instruções de como fazer isso [aqui](web-sites-publish-source-control-git.md#step4).

2. Adicione seu Site do Azure como uma referência remota git.

		git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Implante enviando por push à referência remota git.

		git push azure master

4. Em poucos segundos, o git terminará a publicação do seu aplicativo
Web e iniciará um navegador onde você pode ver seu trabalho útil
em execução no Azure!

## <a name="_Toc395637775"></a>Próximas etapas

Parabéns! Você acabou de criar seu primeiro aplicativo Web Express do Node.js
usando o Banco de Dados de Documentos do Azure e o publicou nos Sites do Azure.

O código-fonte para o aplicativo de referência completo pode ser baixado [aqui](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Portal de Gerenciamento do Azure]: http://portal.azure.com

<!--HONumber=49-->