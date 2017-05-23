---
title: "Saiba mais sobre o Node.js — Tutorial do Node.js para Azure Cosmos DB | Microsoft Docs"
description: Saiba mais sobre o Node.js! O tutorial explica como usar o BD Cosmos do Microsoft Azure para armazenar e acessar dados de um aplicativo Web do Node.js Express hospedado em sites do Azure.
keywords: Desenvolvimento de aplicativos, tutorial de banco de dados, aprender node.js, tutorial do node.js, banco de dados de documentos, azure, Microsoft azure
services: cosmosdb
documentationcenter: nodejs
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 12/16/2016
ms.author: syamk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 20b04f07581354144ef3dd3fc98da10cbff78e63
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="_Toc395783175"></a>Compilar um aplicativo Web Node.js usando o Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [.NET para MongoDB](documentdb-mongodb-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Este tutorial do Node.js mostra a você como usar o Azure Cosmos DB para armazenar e acessar dados de um aplicativo Express do Node.js hospedado nos Sites do Azure. Você cria um aplicativo simples de gerenciamento de tarefas baseado na Web, um aplicativo ToDo, que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas como documentos JSON no Azure Cosmos DB. Este tutorial o orienta durante a criação e a implantação do aplicativo e explica o que está acontecendo em cada trecho de código.

![Captura de tela do aplicativo Minha lista de tarefas pendentes criado neste tutorial](./media/documentdb-nodejs-application/image1.png)

Não há tempo para concluir o tutorial e deseja apenas a solução completa? Não é um problema, você pode obter a solução de exemplo completo da [GitHub][GitHub]. Leia o arquivo [Leiame](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) para obter instruções sobre como executar o aplicativo.

## <a name="_Toc395783176"></a>Pré-requisitos
> [!TIP]
> Este tutorial do Node.js presume que você tenha alguma experiência anterior com o Node.js e sites do Azure.
> 
> 

Antes de seguir as instruções deste artigo, verifique se você possui o seguinte:

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

   OU

   Uma instalação local do [Emulador do Azure Cosmos DB](documentdb-nosql-local-emulator.md).
* [Node.js][Node.js] versão v0.10.29 ou superior.
* [Gerador expresso](http://www.expressjs.com/starter/generator.html) (você pode instalá-lo por meio de `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB
Vamos começar criando uma conta do Azure Cosmos DB. Se você já tiver uma conta ou se estiver usando o Emulador do Azure Cosmos DB para este tutorial, pule para a [Etapa 2: criar um novo aplicativo do Node.js](#_Toc395783178).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Etapa 2: Aprender a criar um novo aplicativo Node.js
Agora vamos aprender a criar um projeto Hello World Node.js básico usando a estrutura [Express](http://expressjs.com/) .

1. Abra seu terminal favorito, como o prompt de comando do Node.js.
2. Navegue até o diretório no qual você deseja armazenar o novo aplicativo.
3. Use o gerador expresso para gerar um novo aplicativo chamado **tarefas**.
   
        express todo
4. Abra o novo diretório **tarefas** e instale as dependências.
   
        cd todo
        npm install
5. Execute seu novo aplicativo.
   
        npm start
6. Veja seu novo aplicativo navegando em seu navegador até [http://localhost:3000/](http://localhost:3000).
   
    ![Saiba mais sobre o Node.js — captura de tela do aplicativo Hello World em uma janela do navegador](./media/documentdb-nodejs-application/image12.png)

    Em seguida, para interromper o aplicativo, pressione CTRL+C na janela do terminal e clique em **y** para finalizar o trabalho em lotes.

## <a name="_Toc395783179"></a>Etapa 3: Instalar módulos adicionais
O arquivo **package.json** é um dos arquivos criados na raiz do projeto. Esse arquivo contém uma lista dos módulos adicionais que são necessários para seu aplicativo do Node.js. Posteriormente, ao implantar esse aplicativo em um site do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo. Ainda precisamos instalar mais dois pacotes para este tutorial.

1. De volta ao terminal, instale o módulo **async** via npm.
   
        npm install async --save
2. Instale o módulo **documentdb** via npm. Esse é o módulo em que toda a mágica do Banco de Dados de Documentos acontece.
   
        npm install documentdb --save
3. Uma verificação rápida do arquivo **package.json** do aplicativo deve mostrar os módulos adicionais. Esse arquivo informará ao Azure quais pacotes para baixar e instalar ao executar seu aplicativo. Ele deve se parecer com o exemplo a seguir.
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    Isso informa ao Nó (e ao Azure mais tarde) que seu aplicativo depende desses módulos adicionais.

## <a name="_Toc395783180"></a>Etapa 4: Usar o serviço do Azure Cosmos DB em um aplicativo de nó
Isso cuida de toda a instalação e configuração inicial. Agora vamos ao motivo de estarmos aqui, ou seja, para gravar algum código usando o Azure Cosmos DB.

### <a name="create-the-model"></a>Criar o modelo
1. No diretório do projeto, crie um novo diretório chamado **modelos** no mesmo diretório que o arquivo package.json.
2. No diretório **models**, criar um novo arquivo chamado **taskDao.js**. Esse arquivo conterá o modelo para as tarefas criadas pelo seu aplicativo.
3. No mesmo diretório **models**, crie um novo arquivo chamado **docdbUtils.js**. Esse arquivo conterá algum código útil e reutilizável, que será usado em todo o nosso aplicativo. 
4. Copie o seguinte código para **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
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
   
   > [!TIP]
   > createCollection aceita um parâmetro de requestOptions opcional que pode ser usado para especificar o Tipo de Oferta para a Coleção. Se nenhum valor de requestOptions.offerType for fornecido, a coleção será criada usando o Tipo de Oferta padrão.
   > 
   > Para saber mais sobre os tipos de oferta do Azure Cosmos DB, consulte [Níveis de desempenho no Azure Cosmos DB](documentdb-performance-levels.md) 
   > 
   > 
5. Salve e feche o arquivo **docdbUtils.js** .
6. No início do arquivo **taskDao.js**, adicione o seguinte código para fazer referência a **DocumentDBClient** e **docdbUtils.js** criados anteriormente:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. Em seguida, você adicionará o código para definir e exportar o objeto da tarefa. Ele é responsável pela inicialização do nosso objeto de Tarefa e por configurar o Banco de Dados e o Conjunto de Documentos que usaremos.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto da Tarefa, que permitem interações com os dados armazenados no Azure Cosmos DB.
   
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
                    query: 'SELECT * FROM root r WHERE r.id = @id',
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
9. Salve e feche o arquivo **taskDao.js** . 

### <a name="create-the-controller"></a>Criar o controlador
1. No diretório **rotas** do projeto, crie um novo arquivo chamado **tasklist.js**. 
2. Adicione os seguintes códigos ao **tasklist.js**. Ele carrega os módulos DocumentDBClient e assíncrono, que são usados pelo **tasklist.js**. Isso também definiu a função de **TaskList**, que é transmitida a uma instância do objeto **Task** definido anteriormente:
   
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
4. Salve e feche o arquivo **tasklist.js** .

### <a name="add-configjs"></a>Adicionar config.js
1. No diretório do projeto, crie um novo arquivo chamado **config.js**.
2. Adicione o seguinte ao **config.js**. Isso define as configurações e valores necessários para nosso aplicativo.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. No arquivo **config.js**, atualize os valores de HOST e de AUTH_KEY usando os valores encontrados na folha Chaves de sua conta do Azure Cosmos DB no [Portal do Microsoft Azure](https://portal.azure.com).
4. Salve e feche o arquivo **config.js** .

### <a name="modify-appjs"></a>Modificar app.js
1. No diretório do projeto, abra o arquivo **app.js** . Esse arquivo foi criado anteriormente, quando o aplicativo Web Express foi criado.
2. Adicione o seguinte código à parte superior do **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Esse código define o arquivo de configuração a ser usado e continua a ler valores desse arquivo em algumas variáveis que usaremos em breve.
4. Substitua as duas linhas seguintes no arquivo **app.js** :
   
        app.use('/', index);
        app.use('/users', users); 
   
      pelo trecho a seguir:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Essas linhas definem uma nova instância do nosso objeto **TaskDao**, com uma nova conexão ao Azure Cosmos DB (usando os valores lidos a partir de **config.js**), inicialize o objeto de tarefa e associe ações do formulário aos métodos no nosso controlador **TaskList**. 
6. Por fim, salve e feche o arquivo **app.js** ; está praticamente pronto.

## <a name="_Toc395783181"></a>Etapa 5: Criar uma interface do usuário
Agora vamos voltar a atenção para criar a interface do usuário, desse modo, um usuário pode realmente interagir com nosso aplicativo. O aplicativo Express que criamos usa **Jade** como mecanismo de exibição. Para obter mais informações sobre o Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar a [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante. 
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

    Isso instrui o mecanismo **Jade** a renderizar um HTML para nosso aplicativo e cria um **bloco** chamado **content**, em que podemos fornecer o layout para nossas páginas de conteúdo.
    Salve e feche o arquivo **layout.jade** .

3. Agora, abra o arquivo **index.jade** , o modo de exibição que será usado pelo nosso aplicativo, e substitua o conteúdo do arquivo pelo seguinte:
   
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
    O primeiro formulário contém uma tabela para nossos dados e um botão que permite atualizar itens pelo lançamento do método **/completetask** de nosso controlador.
    O segundo formulário contém dois campos de entrada e um botão que nos permite criar um novo item ao ser lançado o método **/addtask** do nosso controlador.
   
    Isso deve ser tudo o que precisamos para que nosso aplicativo funcione.
4. Abra o arquivo **style.css** no diretório **public\stylesheets** e substitua o código pelo seguinte:
   
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
   
    Salve e feche esse arquivo **style.css** .

## <a name="_Toc395783181"></a>Etapa 6: Execute o seu aplicativo localmente
1. Para testar o aplicativo no computador local, execute `npm start` no terminal para iniciar o aplicativo e atualize a página do navegador [http://localhost:3000](http://localhost:3000). Agora a página deve ser semelhante à seguinte imagem:
   
    ![Captura de tela do aplicativo MyTodo List em uma janela do navegador](./media/documentdb-nodejs-application/image18.png)

    > [!TIP]
    > Se receber um erro sobre o recuo no arquivo layout.jade ou o arquivo index.jade, verifique se as duas primeiras linhas em ambos os arquivos estão justificadas à esquerda, sem espaços. Se houver espaços antes das duas primeiras linhas, remova-os, salve os dois arquivos e atualize a janela do navegador. 

2. Use os campos Item, Nome do Item e Categoria para inserir uma nova tarefa e clique em **Adicionar Item**. Isso cria um documento no Azure Cosmos DB com essas propriedades. 
3. A página deverá ser atualizada para exibir o item recém-criado na lista de Tarefas Pendentes.
   
    ![Captura de tela do aplicativo com um novo item na lista de Tarefas pendentes](./media/documentdb-nodejs-application/image19.png)
4. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir e clicar em **Atualizar tarefas**. Isso atualiza o documento que você já criou.

5. Para interromper o aplicativo, pressione CTRL+C na janela do terminal e clique em **Y** para finalizar o trabalho em lotes.

## <a name="_Toc395783182"></a>Etapa 7: implantar seu projeto de desenvolvimento de aplicativo nos sites do Azure
1. Se ainda não o fez, habilite um repositório git do seu site do Azure. Encontre instruções sobre como fazer isso no tópico [Implantação GIT Local no Serviço de Aplicativo do Azure](../app-service-web/app-service-deploy-local-git.md) .
2. Adicione seu site do Azure como um git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Implante o envio por push para o computador remoto.
   
        git push azure master
4. Em poucos segundos, o git terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

    Parabéns! Você acabou de criar seu primeiro Aplicativo Web Express do Node.js usando o Azure Cosmos DB e o publicou nos sites do Azure.

    Se você quiser baixar ou consultar o aplicativo de referência completa para este tutorial, ele pode ser baixado do [GitHub][GitHub].

## <a name="_Toc395637775"></a>Próximas etapas

* Quer executar testes de desempenho e escala com o Azure Cosmos DB? Confira [teste de desempenho e escalabilidade com o Azure Cosmos DB](documentdb-performance-testing.md)
* Saiba como [monitorar uma conta do Azure Cosmos DB](documentdb-monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Explore a [Documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app


