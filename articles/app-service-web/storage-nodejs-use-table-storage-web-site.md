---
title: Aplicativo Web Node.js com o Serviço Tabela do Azure
description: Esse tutorial ensina a usar o serviço Tabela do Azure para armazenar dados de um aplicativo Node.js hospedado em Aplicativos Web do Serviço de Aplicativo do Azure.
tags: azure-portal
services: app-service\web, storage
documentationcenter: nodejs
author: rmcmurray
manager: wpickett
editor: ''

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/11/2016
ms.author: robmcm

---
# Aplicativo Web Node.js com o Serviço Tabela do Azure
## Visão geral
Este tutorial mostra como usar o serviço Tabela fornecido pelo Gerenciamento de Dados do Azure para armazenar e acessar dados em um aplicativo de [nó] hospedado nos Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este tutorial pressupõe que você tenha alguma experiência anterior usando nó e [Git].

Você aprenderá:

* Como usar npm (gerenciador de pacote de nós) para instalar os módulos do nó
* Como trabalhar com o serviço de Tabela do Azure
* Como usar a CLI do Azure para criar um aplicativo da web.

Seguindo este tutorial, você criará um aplicativo simples de “lista de tarefas” baseado na web, que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas no serviço Tabela.

Aqui está o aplicativo concluído:

![Uma página da Web que exibe uma lista de tarefas vazia][node-table-finished]

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## Pré-requisitos
Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

* [node] versão 0.10.24 ou superior
* [Git]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Criar uma conta de armazenamento
Crie uma conta de armazenamento do Azure. O aplicativo usará essa conta para armazenar os itens pendentes.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique no ícone **Novo** na parte inferior esquerda do portal e clique em **Dados + Armazenamento** > **Armazenamento**. Dê um nome exclusivo à conta de armazenamento e crie um novo [grupo de recursos](../resource-group-overview.md) para ela.
   
      ![Botão Novo](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)
   
    Quando a conta de armazenamento tiver sido criada, o botão **Notificações** piscará **ÊXITO** em verde e a folha da conta de armazenamento será aberta para mostrar que ela pertence ao novo grupo de recursos que você criou.
3. Na folha da conta de armazenamento, clique em **Configurações** > **Chaves**. Copie a chave de acesso primário para a área de transferência.
   
    ![Chave de acesso][portal-storage-access-keys]

## Instalar módulos e gerar scaffolding
Nesta seção você criará um novo aplicativo de nó e usará o npm para adicionar pacotes de módulo. Para este aplicativo, você usará os módulos [Express] e [Azure]. O módulo Express fornece uma estrutura de Controlador da Exibição do Modelo para o nó, enquanto que os módulos do Azure fornecem a conectividade ao serviço de Tabela.

### Instalar o express e gerar scaffolding
1. Na linha de comando, crie um novo diretório chamado **tasklist** e alterne para esse diretório.
2. Digite o comando a seguir para instalar o módulo Express.
   
        npm install express-generator@4.2.0 -g
   
    Dependendo do sistema operacional, talvez seja necessário colocar ‘sudo’ antes do comando:
   
        sudo npm install express-generator@4.2.0 -g
   
    A saída é semelhante a este exemplo:
   
        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)
   
   > [!NOTE]
   > O parâmetro '-g' instala o módulo de forma global. Desse modo, podemos usar **express** para gerar o scaffolding do aplicativo Web sem precisar digitar informações adicionais de caminho.
   > 
   > 
3. Para criar o scaffolding do aplicativo, digite o comando **express**:
   
        express
   
    A saída deste comando é semelhante a este exemplo:
   
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
   
    Agora, você terá vários novos diretórios e arquivos no diretório **tasklist**.

### Instalar módulos adicionais
Um dos arquivos criados por **express** é **package.json**. Esse arquivo contém uma lista das dependências do módulo. Posteriormente, quando você implantar esse aplicativo nos Aplicativos Web do Serviço de Aplicativo, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure.

Na linha de comando, digite o comando a seguir para instalar os módulos descritos no arquivo **package.json**. Talvez seja necessário usar 'sudo'.

    npm install

A saída deste comando é semelhante a este exemplo:

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


Em seguida, insira o seguinte comando para instalar os módulos [azure], [node-uuid], [nconf] e [async]\:

    npm install azure-storage node-uuid async nconf --save

O sinalizador **--save** adiciona entradas para esses módulos ao arquivo **package.jsohn**.

A saída deste comando é semelhante a este exemplo:

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## Criar o aplicativo
Agora estamos prontos para criar o aplicativo.

### Criar um modelo
Um *modelo* é um objeto que representa os dados no seu aplicativo. Para o aplicativo, o único modelo é um objeto de tarefa, que representa um item da lista de tarefas pendentes. As tarefas terão os seguintes campos:

* PartitionKey
* RowKey
* nome (cadeia de caracteres)
* categoria (cadeia de caracteres)
* concluído (booleano)

**PartitionKey** e **RowKey** são usados pelo serviço Tabela como chaves de tabela. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

1. No diretório **tasklist**, crie um novo diretório com nome **models**.
2. No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.
3. No início do arquivo **task.js**, adicione o seguinte código para fazer a referência às bibliotecas necessárias:
   
        var azure = require('azure-storage');
          var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;
4. Adicione o código a seguir para definir e exportar o objeto de tarefa. Este objeto é responsável por se conectar à tabela.
   
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
5. Adicione o seguinte código para definir métodos adicionais no objeto de tarefa, os quais permitem interações com os dados armazenados na tabela:
   
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

### Criar um controlador
O *controlador* trata as solicitações HTTP e renderiza a resposta HTML.

1. No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.
2. Adicione os seguintes códigos ao **tasklist.js**. Isso carrega os módulos azure e async, que são usados por **tasklist.js**. Isso também define a função de **TaskList**, que é transmitida a uma instância do objeto da **Tarefa** definido anteriormente:
   
        var azure = require('azure-storage');
        var async = require('async');
   
        module.exports = TaskList;
3. Defina um objeto **TaskList**.
   
        function TaskList(task) {
          this.task = task;
        }
4. Adicione os seguintes métodos à **TaskList**:
   
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
            var self = this;
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

### Modificar app.js
1. No diretório **tasklist**, abra o arquivo **app.js**. Este arquivo foi criado anteriormente usando o comando **express**.
2. No início do arquivo, adicione o seguinte para carregar o módulo azure, definir o nome da tabela, a chave de partição e definir as credenciais de armazenamento usadas neste exemplo:
   
        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");
   
   > [!NOTE]
   > nconf carregará os valores de configuração de uma das variáveis de ambiente ou o arquivo **config.json**, que será criado posteriormente.
   > 
   > 
3. No arquivo app.js, role para baixo até onde for capaz de visualizar a seguinte linha:
   
        app.use('/', routes);
        app.use('/users', users);
   
    Substitua as linhas acima pelo código mostrado abaixo. Será inicializada uma instância de <strong>Tarefa</strong> com uma conexão à sua conta de armazenamento. Ela será transmitida à <strong>TaskList</strong>, que a usará para se comunicar com o serviço Tabela:
   
        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
4. Salve o arquivo **app.js**.

### Modificar a exibição de índice
1. Abra o arquivo **tasklist/views/index.jade** em um editor de texto.
2. Substitua todo o conteúdo do arquivo pelo código a seguir. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.
   
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

### Modificar o layout global
O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar a [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um kit de ferramentas que facilita a criação de um aplicativo Web com uma aparência interessante.

Baixe e extraia os arquivos para a [Twitter Bootstrap](http://getbootstrap.com/). Copie o arquivo **bootstrap.min.css** da pasta **css** do Bootstrap para o diretório **public/stylesheets** do seu aplicativo.

Na pasta **views**, abra o **layout.jade** e substitua todo o seu conteúdo pelo seguinte:

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

### Criar um arquivo de configuração
Para executar o aplicativo localmente, colocaremos as credenciais de Armazenamento do Azure em um arquivo de configuração. Crie um arquivo chamado **config.json* * com o JSON a seguir:

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Substitua **nome da conta de armazenamento** pelo nome da conta de armazenamento criada anteriormente e substitua **chave de acesso de armazenamento** pela chave de acesso primário da sua conta de armazenamento. Por exemplo:

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Salve esse arquivo em *um nível de diretório superior* ao do diretório **tasklist**, como este:

    parent/
      |-- config.json
      |-- tasklist/

A razão para isso é evitar a verificação do arquivo de configuração no controle de origem, onde ele pode se tornar público. Ao implantar o aplicativo no Azure, usaremos as variáveis de ambiente em vez de um arquivo de configuração.

## Executar o aplicativo localmente
Execute as etapas a seguir para testar o aplicativo em seu computador local:

1. Da linha de comando, mude os diretórios para o diretório **tasklist**.
2. Use o seguinte comando para iniciar o aplicativo localmente:
   
        npm start
3. Abra o navegador da Web e acesse http://127.0.0.1:3000.
   
    A saída da página Web semelhante ao exemplo a seguir aparece.
   
    ![Uma página da Web que exibe uma lista de tarefas vazia][node-table-finished]
4. Para criar um novo item pendente, insira um nome e uma categoria e clique em **Adicionar item**.
5. Para marcar uma tarefa como concluída, marque **Concluir** e clique em **Atualizar tarefas**.
   
    ![Uma imagem do novo item na lista de tarefas][node-table-list-items]

Mesmo que o aplicativo seja executado localmente, ele armazena os dados no serviço Tabela do Azure.

## Implantar seu aplicativo no Azure
As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo aplicativo Web no Serviço de Aplicativo do Azure e, em seguida, usam o Git para implantar o aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

> [!NOTE]
> Essas etapas também podem ser executadas usando o [Portal do Azure](https://portal.azure.com/). Confira [Criar e implantar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure].
> 
> Se esse for o primeiro aplicativo web que você criou, use o Portal do Azure para implantar o aplicativo.
> 
> 

Para começar, instale a [CLI do Azure] inserindo o seguinte comando na linha de comando:

    npm install azure-cli -g

### Importar configurações de publicação
Nesta etapa, você baixará um arquivo que contém informações sobre sua assinatura.

1. Digite o seguinte comando:
   
        azure account download
   
    Esse comando inicia um navegador e acessa a página de download. Se solicitado, faça o logon usando a conta associada à sua assinatura do Azure.
   
    <!-- ![The download page][download-publishing-settings] -->
   
    O download do arquivo começa automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo. Salve o arquivo e anote o caminho do arquivo.
2. Digite o seguinte comando para importar as configurações:
   
        azure account import <path-to-file>
   
    Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior.
3. Depois que as configurações forem importadas, exclua o arquivo de configurações de publicação. Ele não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

### Criar um aplicativo Web do Serviço de Aplicativo
1. Da linha de comando, mude os diretórios para o diretório **tasklist**.
2. Use o comando a seguir para criar um novo aplicativo Web.
   
        azure site create --git
   
    Será solicitada a informação do nome e do local do aplicativo Web. Forneça um nome exclusivo e selecione a mesma localização geográfica da sua conta de armazenamento do Azure.
   
    O parâmetro `--git` cria um repositório Git no Azure para esse aplicativo Web. Também inicializa um repositório Git no diretório atual (caso não exista) e adiciona um [Git remoto] chamado 'azure', que é usado para publicar o aplicativo no Azure. Por fim, será criado um arquivo **web.config**, que contém as configurações usadas pelo Azure para hospedar aplicativos de nó. Se você omitir o parâmetro `--git` mas se o diretório tiver um repositório Git, o comando ainda criará o 'azure' remoto.
   
    Quando este comando for concluído, você verá uma saída semelhante à seguinte. Observe que a linha que começa com **Site criado em** contém a URL do aplicativo Web.
   
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
   
   > [!NOTE]
   > Se esse for o primeiro aplicativo Web do Serviço de Aplicativo da sua assinatura, você será instruído a usar o Portal do Azure para criar o aplicativo Web. Para obter mais informações, consulte [Criar e implantar um aplicativo Web Node.js no Serviço de Aplicativo do Azure].
   > 
   > 

### Configurar variáveis de ambiente
Nesta etapa, você irá adicionar variáveis de ambiente à configuração do aplicativo Web no Azure. Na linha de comando, insira o seguinte:

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Substitua **<nome da conta de armazenamento>** com o nome do armazenamento de conta que você criou anteriormente e substitua **<chave de acesso de armazenamento>** com a chave de acesso primário para sua conta de armazenamento. (Use os mesmos valores do arquivo config.json que você criou anteriormente.)

Como alternativa, você pode definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com/):

1. Abra a folha do aplicativo Web clicando em **Procurar** > **Aplicativos Web** > nome do aplicativo Web.
2. Na folha do seu aplicativo Web, clique em **Todas as configurações** > **Configurações do aplicativo**.
   
     <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->
3. Role para baixo até a seção de **Configurações do aplicativo** seção e adicione os pares chave/valor.
   
     ![Configurações do aplicativo](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)
4. Clique em **SALVAR**.

### Publicar o aplicativo
Para publicar o aplicativo, confirme os arquivos de código no Git e envie por push ao azure/mestre.

1. Configure suas credenciais de implantação.
   
        azure site deployment user set <name> <password>
2. Adicione e confirme os arquivos do aplicativo.
   
        git add .
        git commit -m "adding files"
3. Envie a confirmação para o aplicativo Web do Serviço de Aplicativo:
   
        git push azure master
   
    Use **mestre** como a ramificação de destino. No final da implantação, você verá uma instrução semelhante a este exemplo:
   
        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
          * [new branch]      master -> master
4. Quando a operação de envio for concluída, vá até a URL do aplicativo Web retornada anteriormente pelo comando `azure create site` para exibir o aplicativo.

## Próximas etapas
Embora as etapas neste artigo descrevam como usar o Serviço Tabela para armazenar informações, você também pode usar o MongoDB. Confira [Aplicativo Web Node.js com o MongoDB] para obter mais informações.

## Recursos adicionais
[CLI do Azure]

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Criar e implantar um aplicativo Web Node.js no Serviço de Aplicativo do Azure]: web-sites-nodejs-develop-deploy-mac.md
[Criar e implantar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[node]: http://nodejs.org
[nó]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remoto]: http://git-scm.com/docs/git-remote

[Aplicativo Web Node.js com o MongoDB]: web-sites-nodejs-store-data-mongodb.md
[CLI do Azure]: ../xplat-cli-install.md

[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md

<!-- Image References -->

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

<!---HONumber=AcomDC_0817_2016-->