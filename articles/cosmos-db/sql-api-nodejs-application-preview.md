---
title: Compilar um aplicativo Web do Node.js para o banco de dados do Azure Cosmos DB | Microsoft Docs
description: Este tutorial do Node.js explora como usar o Microsoft Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web do Node.js Express hospedado em sites do Azure.
keywords: Desenvolvimento de aplicativos, tutorial de banco de dados, aprender Node.js, tutorial do Node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608963"
---
# <a name="_Toc395783175"></a>Criar um aplicativo Web Node.js usando o Azure Cosmos DB e o SDK do Node.js (Versão prévia)
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js – v2.0 Preview](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Este tutorial do Node.js mostra como usar a conta da API do SQL do Azure Cosmos DB para armazenar e acessar dados de um aplicativo Expresso do Node.js hospedado em sites do Azure. Neste tutorial, você criará um aplicativos baseado na Web simples (aplicativo Todo – lista de tarefas pendentes), que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas como documentos JSON no Azure Cosmos DB. A imagem abaixo mostra uma captura de tela do aplicativo de lista de tarefas pendentes:

![Captura de tela do aplicativo Minha lista de tarefas pendentes criado neste tutorial](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Este tutorial demonstra como criar uma conta de API do SQL do Azure Cosmos DB usando o portal do Azure. Em seguida, você compila e executa um aplicativo Web que se baseia no SDK do Node.js para criar um banco de dados e um contêiner e adicionar itens ao contêiner. Este tutorial usa a versão 2.0 do SDK do Node.js, que está atualmente em versão prévia.

Você também pode obter o exemplo completo no [GitHub][GitHub]. Leia o arquivo [Leiame](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) para obter instruções sobre como executar o aplicativo.

## <a name="_Toc395783176"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você possui o seguinte:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] versão 6.10 ou superior.
* [Gerador expresso](http://www.expressjs.com/starter/generator.html) (você pode instalá-lo por meio de `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB
Vamos começar criando uma conta do Azure Cosmos DB. Se você já tiver uma conta ou se estiver usando o Emulador do Azure Cosmos DB para este tutorial, pule para a [Etapa 2: criar um novo aplicativo do Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Etapa 2: criar um novo aplicativo do Node.js
Agora vamos aprender a criar um projeto Hello World Node.js básico usando a estrutura [Express](http://expressjs.com/) .

1. Abra seu terminal favorito, como o prompt de comando do Node.js.
2. Navegue até o diretório no qual você deseja armazenar o novo aplicativo.
3. Use o gerador expresso para gerar um novo aplicativo chamado **tarefas**.

   ```bash
   express todo
   ```
4. Abra o novo diretório **tarefas** e instale as dependências.

   ```bash
   cd todo
   npm install
   ```
5. Execute seu novo aplicativo.

   ```bash
   npm start
   ```

6. Veja seu novo aplicativo navegando em seu navegador até [http://localhost:3000](http://localhost:3000).
   
    ![Saiba mais sobre o Node.js — captura de tela do aplicativo Hello World em uma janela do navegador](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Interrompa o aplicativo usando CTRL+C na janela do terminal e clicando em **Y** para finalizar o trabalho em lotes.

## <a name="_Toc395783179"></a>Etapa 3: Instalar os módulos obrigatórios

O arquivo **package.json** é um dos arquivos criados na raiz do projeto. Esse arquivo contém uma lista dos módulos adicionais que são necessários para seu aplicativo do Node.js. Posteriormente, ao implantar esse aplicativo em sites do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para dar suporte ao seu aplicativo. Você ainda precisa instalar mais dois pacotes para este tutorial.

1. Abra o terminal e instale o módulo **async** via npm.

   ```bash
   npm install async --save
   ```

2. Instale o módulo **@azure/cosmos** usando o npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Etapa 4: Usando o serviço do Azure Cosmos DB em um aplicativo Node
Agora que você concluiu a instalação e a configuração iniciais, escreverá o código exigido pelo aplicativo de lista de tarefas pendentes para se comunicar com o Azure Cosmos DB.

### <a name="create-the-model"></a>Criar o modelo
1. Na raiz do diretório do projeto, crie um novo diretório chamado **modelos**  

2. No diretório **models**, criar um novo arquivo chamado **taskDao.js**. Esse arquivo contém o código necessário para criar o banco de dados e o contêiner e define métodos para ler, atualizar, criar e localizar as tarefas no Azure Cosmos DB. 

3. Copie o código a seguir no arquivo **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Salve e feche o arquivo **taskDao.js** .  

### <a name="create-the-controller"></a>Criar o controlador

1. No diretório **rotas** do projeto, crie um novo arquivo chamado **tasklist.js**.  

2. Adicione os seguintes códigos ao **tasklist.js**. Ele carrega os módulos CosmosClient e assíncrono, que são usados pelo **tasklist.js**. Isso também define a classe **TaskList**, que é transmitida a uma instância do objeto de **TaskDao** definido anteriormente:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Salve e feche o arquivo **tasklist.js** .

### <a name="add-configjs"></a>Adicionar config.js

1. Na raiz do diretório do projeto, crie um novo arquivo chamado **config.js**. 

2. Adicione o código a seguir ao arquivo **config.js**. Esse código define as configurações e valores necessários para nosso aplicativo.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. No arquivo **config.js**, atualize os valores de HOST e de AUTH_KEY usando os valores encontrados na página Chaves de sua conta do Azure Cosmos DB no [Portal do Microsoft Azure](https://portal.azure.com). 

4. Salve e feche o arquivo **config.js** .

### <a name="modify-appjs"></a>Modificar app.js
1. No diretório do projeto, abra o arquivo **app.js** . Esse arquivo foi criado anteriormente, quando o aplicativo Web Express foi criado.  

2. Adicione o código a seguir ao arquivo **app.js**. Esse código define o arquivo de configuração a ser usado e continua a ler valores desse arquivo em algumas variáveis que usaremos em breve. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Por fim, salve e feche o arquivo **app.js** ; está praticamente pronto.

## <a name="_Toc395783181"></a>Etapa 5: Criar uma interface do usuário
Agora vamos voltar a atenção para criar a interface do usuário, desse modo, um usuário pode realmente interagir com nosso aplicativo. O aplicativo Express que criamos usa **Jade** como mecanismo de exibição. Para obter mais informações sobre Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar a [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante.  

2. Abra o arquivo **layout.jade** encontrado na pasta **views** e substitua o conteúdo pelo seguinte:

   ```html
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
   ```

    Isso instrui o mecanismo **Jade** a renderizar um HTML para nosso aplicativo e cria um **bloco** chamado **content**, em que podemos fornecer o layout para nossas páginas de conteúdo.

    Salve e feche o arquivo **layout.jade** .

3. Agora, abra o arquivo **index.jade** , o modo de exibição que será usado pelo nosso aplicativo, e substitua o conteúdo do arquivo pelo seguinte:

   ```html
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
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

Isso estende o layout e fornece conteúdo para o espaço reservado **content** que vimos no arquivo **layout.jade** anteriormente.
   
Nesse layout criamos dois formulários HTML.

O primeiro formulário contém uma tabela para nossos dados e um botão que permite atualizar itens pelo lançamento do método **/completeTask** de nosso controlador.
    
O segundo formulário contém dois campos de entrada e um botão que nos permite criar um novo item ao ser lançado o método **/addtask** do nosso controlador.

Isso deve ser tudo o que precisamos para que nosso aplicativo funcione.

## <a name="_Toc395783181"></a>Etapa 6: Execute o seu aplicativo localmente
1. Para testar o aplicativo no computador local, execute `npm start` no terminal para iniciar o aplicativo e atualize a página do navegador [http://localhost:3000](http://localhost:3000). Agora a página deve ser semelhante à seguinte imagem:
   
    ![Captura de tela do aplicativo MyTodo List em uma janela do navegador](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Se receber um erro sobre o recuo no arquivo layout.jade ou o arquivo index.jade, verifique se as duas primeiras linhas em ambos os arquivos estão justificadas à esquerda, sem espaços. Se houver espaços antes das duas primeiras linhas, remova-os, salve os dois arquivos e atualize a janela do navegador. 

2. Use os campos Item, Nome do Item e Categoria para inserir uma nova tarefa e clique em **Adicionar Item**. Isso cria um documento no Azure Cosmos DB com essas propriedades. 
3. A página deverá ser atualizada para exibir o item recém-criado na lista de Tarefas Pendentes.
   
    ![Captura de tela do aplicativo com um novo item na lista de Tarefas pendentes](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir e clicar em **Atualizar tarefas**. Isso atualiza o documento que você já criou e o remove da visualização.

5. Para interromper o aplicativo, pressione CTRL+C na janela do terminal e clique em **Y** para finalizar o trabalho em lotes.

## <a name="_Toc395783182"></a>Etapa 7: implantar seu projeto de desenvolvimento de aplicativo nos sites do Azure
1. Se ainda não o fez, habilite um repositório git do seu site do Azure. Encontre instruções sobre como fazer isso no tópico [Implantação GIT Local no Serviço de Aplicativo do Azure](../app-service/app-service-deploy-local-git.md) .
2. Adicione seu site do Azure como um git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Implante o envio por push para o computador remoto.
   
        git push azure master
4. Em poucos segundos, o git terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

    Parabéns! Você acabou de criar seu primeiro Aplicativo Web Express do Node.js usando o Azure Cosmos DB e o publicou nos sites do Azure.

    Se você quiser baixar ou consultar o aplicativo de referência completa para este tutorial, ele pode ser baixado do [GitHub][GitHub].

## <a name="_Toc395637775"></a>Próximas etapas

* Quer executar testes de desempenho e escala com o Azure Cosmos DB? Confira [teste de desempenho e escalabilidade com o Azure Cosmos DB](performance-testing.md)
* Saiba como [monitorar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Explore a [Documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

