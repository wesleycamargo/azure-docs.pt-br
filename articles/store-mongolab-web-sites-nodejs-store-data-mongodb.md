<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com"></tags>

# Criar um aplicativo do Node.js no Azure com MongoDB usando o suplemento MongoLab

*Por Eric Sedor, MongoLab*

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1.  [Provisionar o banco de dados][] O complemento [MongoLab][] da Azure Store fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
2.  [Criar o aplicativo][] - Esse será um aplicativo simples do Node. js para manter uma lista de tarefas.
3.  [Implantar o aplicativo][] - com a reunião de alguns ganchos de configuração, a implantação do nosso código será moleza.
4.  [Gerenciar o banco de dados][] - finalmente, mostraremos o portal de gerenciamento de banco de dados baseado na web do MongoLab, onde você poderá pesquisar, visualizar e modificar dados facilmente.

A qualquer momento durante este tutorial, fique à vontade para mandar um e-mail para [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][] em caso de dúvidas.

Antes de continuar, certifique-se de que você tem o seguinte instalado:

-   [Node.js][] Versão 0.8.14+

-   [Git][]

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Início rápido

Se você tiver alguma familiaridade com o Azure Store, use esta seção para fazer um início rápido. Caso contrário, vá para [Provisionar o banco de dados][] abaixo.

1.  Abra a Azure Store.
    ![Store][]
2.  Clique no complemento.
    ![MongoLab][1]
3.  Clique no complemento MongoLab na lista Complementos e, em seguida, clique em **Informações de Conexão**.
    ![ConnectionInfoButton][]
4.  Copie o MONGOLAB\_URI para a sua área de transferência.
    ![ConnectionInfoScreen][]
    **Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.**
5.  Adicione o valor à lista Cadeias de Conexão no menu Configuração do seu aplicativo Web do Azure:
    ![WebSiteConnectionStrings][]
6.  Para **nome**, digite MONGOLAB\_URI.
7.  Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
8.  Selecione **Personalizado** na caixa suspensa Tipo (em vez do padrão **SQLAzure**).
9.  Execute`npm install mongoose` para obter Mongoose, um driver para nós do MongoDB.
10. Configure um gancho em seu código para obter o URI de conexão do MongoLab de uma variável de ambiente e conecte:

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

Observação: O Azure adiciona o prefixo **CUSTOMCONNSTR\_** à cadeia de conexão declarada originalmente e é por isso que o código faz referência a **CUSTOMCONNSTR\_MONGOLAB\_URI.** em vez de a **MONGOLAB\_URI**.

Agora, vamos para o tutorial completo...

## <a name="provision"></a>Provisionar o banco de dados

[WACOM.INCLUDE [howto-provision-mongolab][]]

## <a name="create"></a>Crie o aplicativo

Nesta seção você vai configurar seu ambiente de desenvolvimento e definir o código para aplicativo Web básico usando Node.js, Express e MongoDB. [Express][] fornece uma estrutura de Modelo-Exibição-Controlador para nós, enquanto [Mongoose][] é um driver para a comunicação com o MongoDB.

### Configuração

#### Gerar scaffolding e instalar módulos

1.  Na linha de comando, crie e navegue até o diretório **tasklist**. Esse será o diretório do projeto.
2.  Insira o seguinte comando para instalar o express.

        npm install express -g

    `-g` indica o modo global, que usamos para disponibilizar o módulo **express** sem especificar um caminho de diretório. Se você receber**Error: EPERM, chmod '/usr/local/bin/express'** use **sudo** para executar o npm em um nível de privilégio mais elevado.

    A saída desse comando deve ser semelhante ao seguinte:

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

3.  Para criar o scaffolding que será usado para esse aplicativo, use o comando **express**:

    express

    A saída desse comando deve ser semelhante ao seguinte:

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

4.  Digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída desse comando deve ser semelhante ao seguinte:

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

    O arquivo **package.json** está localizado em um dos arquivos criados pelo comando **express**. Este arquivo contém uma lista dos módulos adicionais que são necessários para um aplicativo Express. Posteriormente, ao implantar esse aplicativo em um site do Azure, esse arquivo será usado para determinar quais módulos precisam ser instalados no Azure para oferecer suporte ao seu aplicativo.

5.  Em seguida, digite o seguinte comando para instalar o módulo Mongoose localmente, bem como para salvar uma entrada para ele no arquivo **package.json**:

        npm install mongoose --save

    A saída desse comando deve ser semelhante ao seguinte:

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

Agora que nosso ambiente e scaffolding estão prontos, ampliaremos o aplicativo básico criado pelo comando **express**, adicionando um arquivo **task.js** que contém o modelo para suas tarefas. Você também vai modificar o **app.js** existente e criar um novo arquivo de controlador **tasklist.js** para usar o modelo.

#### Criar o módulo

1.  No diretório **tasklist**, crie um novo diretório chamado **models**.

2.  No diretório **models**, crie um novo arquivo chamado **task.js**. Esse arquivo vai conter o modelo para as tarefas criadas pelo seu aplicativo.

3.  Adicione o seguinte código ao arquivo **task.js**:

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Salve e feche o arquivo **task.js**.

#### Criar o controlador

1.  No diretório **tasklist/routes**, crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2.  Adicione os seguintes códigos ao **tasklist.js**. Isso carrega o módulo mongoose e o modelo da tarefa definido em **task.js**. A função TaskList é usada para criar a conexão com o servidor MongoDB com base no valor de **connection**, e fornece os métodos **showTasks**, **addTask** e **completeTasks**:

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

3.  Salve o arquivo **tasklist.js**.

#### Modificar a exibição de índice

1.  Altere os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

2.  Substitua o conteúdo do arquivo **index.jade** pelo código abaixo. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.

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

3.  Salve e feche o arquivo **index.jade**.

#### Substituir o app.js

1.  No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.
2.  Adicione o seguinte código no início do arquivo **app.js**. Isso inicializará o **TaskList** com a cadeia de conexão para o servidor do MongoDB:

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    Observe a segunda linha; você acessa uma variável de ambiente que você vai configurar mais tarde, que contém as informações de conexão para a instância mongo. Se você tiver uma instância local do mongo em execução para fins de desenvolvimento, é aconselhável definir temporariamente esse valor como "localhost" em vez de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3.  Localize as linhas que começam com `app.get` e as substitua pelas seguintes:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Isso adiciona as funções definidas no **tasklist.js** como rotas.

4.  Salve o arquivo **app.js**.

## <a name="deploy"></a>Implantar o aplicativo

Agora que o aplicativo foi desenvolvido, é hora de criar um Site do Azure para hospedá-lo, configurar esse site e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu site com esse URI para manter o URI separado do seu código. Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

As etapas desta seção usam as ferramentas de linha de comando do Azure para criar um novo site do Azure e, em seguida, usa o Git para implantar seu aplicativo. Para realizar essas etapas, você deve ter uma assinatura do Azure.

### Instalar as ferramentas de linha de comando do Azure para Mac e Linux

Para instalar as ferramentas de linha de comando, use o seguinte comando:

    npm install azure-cli -g

Se você já tiver instalado o **SDK do Azure para o Node.js** do [Centro de Desenvolvedores do Azure][], então as ferramentas de linha de comando já devem estar instaladas. Para obter mais informações, consulte [Ferramentas de linha de comando do Azure para Mac e Linux][].

Enquanto as ferramentas de linha de comando do Azure foram criadas principalmente para usuários do Mac e do Linux, eles se baseiam em Node. js e devem funcionar em qualquer sistema capaz de executar nós.

### Importar configurações de publicação

Antes de usar as ferramentas de linha de comando com o Azure, você deve primeiro baixar um arquivo que contém informações sobre sua assinatura. Execute as seguintes etapas para fazer o download e importar este arquivo.

1.  Na linha de comando, digite o seguinte comando para iniciar o navegador e vá até a página de download. Se solicitado, faça o login usando a conta associada à sua assinatura.

        azure account download

    ![A página de download][]

    O download do arquivo deve começar automaticamente. Se não, você pode clicar no link no início da página para baixar manualmente o arquivo.

2.  Após o download do arquivo, use o seguinte comando para importar as configurações:

        azure account import <path-to-file>

    Especifique o caminho e o nome do arquivo de configurações de publicação que você baixou na etapa anterior. Quando o comando for concluído, você verá uma saída semelhante à seguinte:

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  Quando a importação for concluída, você deve excluir o arquivo de configurações de publicação, já que ele não é mais necessário e contém informações confidenciais sobre sua assinatura do Azure.

### Criar um novo site e seu código de envio

Criar um site no Azure é muito fácil. Se esse for o seu primeiro site do Azure, você deve usar o portal. Se você já tiver pelo menos um, vá para a etapa 7.

1.  No portal do Azure, clique em **Novo**.
    ![Novo][]
2.  Selecione **Computação \> Site \> Criação Rápida**.
    ![CreateSite][]
3.  Digite um prefixo de URL. Escolha um nome de sua preferência, mas lembre-se de que ele deve ser exclusivo ('mymongoapp' provavelmente não estará disponível).
4.  Clique em **Criar Site**.
5.  Quando a criação do site for concluída, clique no nome do site na lista de sites. O painel do site é exibido.
    ![WebSiteDashboard][]
6.  Clique em **Configurar a publicação do Git** em **visão rápida**e insira seu nome de usuário Git e senha desejados. Você usará essa senha ao enviar para seu site (na etapa 9).
7.  Se você tiver criado seu site usando as etapas acima, o comando a seguir pode concluir o processo. No entanto, se você tiver mais de um site do Azure, ignore as etapas acima e crie um novo site usando o mesmo comando. De seu diretório de projeto **tasklist**:

        azure site create myuniquesitename --git  

    Substitua 'myuniquesitename' pelo nome de site exclusivo para seu site. Se o site for criado como parte do comando, você será solicitado pelo datacenter onde o site está localizado. Selecione o data center geograficamente mais perto de seu banco de dados do MongoLab.

    O parâmetro `--git` criará:
    A. um repositório local de git na pasta de R. um repositório local de Git na pasta **tasklist**, se não houver nenhum.
    A. um [Git remoto][] nomeado 'azure', que será usado para publicar o aplicativo no Azure.
    A. um arquivo [iisnode.yml][], que contém as configurações do Azure para host aplicativos de nó.
    A. um arquivo .gitignore para evitar que a pasta de módulos do nó seja publicada em .git.

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

8.  Use os seguintes comandos para adicionar e, em seguida, submeter arquivos para o repositório Git local:

        git add .
        git commit -m "adding files"

9.  Envie seu código:

        git push azure master  

    Ao enviar as alterações do repositório Git mais recentes para o Site do Azure, você deve especificar que a ramificação de destino é **master**, já que ele é usado para o conteúdo do site. Se for solicitada uma senha, digite a senha que você criou quando configurou o Git de publicação para o site acima.

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

Lembra-se do process.env.CUSTOMCONNSTR\_MONGOLAB\_URI no código? Queremos preencher essa variável de ambiente com o valor fornecido para o Azure durante o provisionamento do banco de dados MongoLab.

#### Obter a cadeia de conexão do MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab][]]

#### Adicionar a cadeia de conexão às variáveis de ambiente do site

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab][]]

## Sucesso!

Execute `azure site browse` do diretório do projeto para abrir automaticamente um navegador, ou abra um navegador e navegue manualmente para o URL do site (myuniquesite.azurewebsites.net):

![Uma página da Web que exibe uma lista de tarefas vazia][]

## <a name="manage"></a>Gerenciar o banco de dados

[WACOM.INCLUDE [howto-access-mongolab-ui][]]

Parabéns! Você acabou de lançar um aplicativo Node. js apoiado por um banco de dados MongoDB hospedado em MongoLab! Agora que o banco de dados MongoLab foi criado, você poderá entrar em contato com [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][]com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda para o MongoDB ou para o próprio driver do nó. Boa sorte!

  [Provisionar o banco de dados]: #provision
  [MongoLab]: http://mongolab.com
  [Criar o aplicativo]: #create
  [Implantar o aplicativo]: #deploy
  [Gerenciar o banco de dados]: #manage
  [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]: mailto:support@mongolab.com
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Centro de Desenvolvedores do Azure]: /pt-br/develop/nodejs/
  [Ferramentas de linha de comando do Azure para Mac e Linux]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/
  [A página de download]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [Novo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Git remoto]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [Uma página da Web que exibe uma lista de tarefas vazia]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md
