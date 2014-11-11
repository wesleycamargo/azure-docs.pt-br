<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Website with SQL Database" pageTitle="Node.js website with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="nodejs" title="Node.js Web Application using the Azure SQL Database" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Aplicativo Web do Node.js usando o Banco de Dados SQL do Azure

Este tutorial mostra como usar o banco de dados SQL fornecido pelo Gerenciamento de Dados do Azure para armazenar e acessar dados em um aplicativo de [nó][nó] hospedado no Azure. Este tutorial pressupõe que você tenha alguma experiência anterior usando nó e [Git][Git].

Você aprenderá a:

-   Como usar o Portal de Gerenciamento do Azure para criar um Site do Azure e um banco de dados SQL

-   Como usar npm (gerenciador de pacote de nós) para instalar os módulos do nó

-   Como trabalhar com um Banco de Dados SQL usando o módulo node-sqlserver

-   Como usar as configurações do aplicativo para especificar valores de tempo de execução para um aplicativo

Seguindo este tutorial, você criará um aplicativo simples de gerenciamento de tarefas baseado na web que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas no Banco de Dados SQL.

Os arquivos do projeto deste tutorial serão armazenados em um diretório chamado **tasklist**, e o aplicativo completo terá um aspecto semelhante ao seguinte:

![Uma página da Web que exibe uma lista de tarefas vazia][Uma página da Web que exibe uma lista de tarefas vazia]

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>O Driver da Microsoft para o Node.JS para SQL Server usado neste tutorial est&aacute; dispon&iacute;vel atualmente como uma vers&atilde;o de visualiza&ccedil;&atilde;o e depende dos componentes de tempo de execu&ccedil;&atilde;o que s&oacute; est&atilde;o dispon&iacute;veis nos sistemas operacionais Microsoft Windows e Azure.</p>
</div>

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Este tutorial faz refer&ecirc;ncia &agrave; pasta <strong>tasklist</strong>. O caminho completo da pasta &eacute; omitido, pois a sem&acirc;ntica de caminho difere entre os sistemas operacionais. Voc&ecirc; deve criar essa pasta em um local de f&aacute;cil acesso em seu sistema de arquivos local, como <strong>~/node/tasklist</strong> ou <strong>c:\node\tasklist</strong></p>
</div>

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Muitas das etapas abaixo mencionam o uso da linha de comando. Para essas etapas, use a linha de comando de seu sistema operacional, como <strong>cmd.exe</strong> (Windows) ou <strong>Bash</strong> (Unix Shell). Nos sistemas OS X, voc&ecirc; pode acessar a linha de comando por meio do aplicativo Terminal.</p>
</div>

## Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

-   [node][nó] versão 0.6.14 ou superior

-   [Git][Git]

-   Bibliotecas do Microsoft SQL Server Native Client - disponíveis como parte do [Microsoft SQL Server 2012 Feature Pack][Microsoft SQL Server 2012 Feature Pack]

-   Um editor de texto

-   Um navegador da Web

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

## Criar site com banco de dados

Siga estas etapas para criar um Site do Azure e um Banco de Dados SQL:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **SITE**, em seguida clique em **CRIAÇÃO PERSONALIZADA**.

    ![Criação personalizada de um novo site][Criação personalizada de um novo site]

    Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados SQL** a partir da lista suspensa **BASE DE DADOS** e selecione o datacenter para seu site na lista suspensa **REGIÃO**. Clique na seta na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][Preencha os detalhes do site]

4.  Insira um valor para o **NOME** do seu banco de dados, selecione a **EDIÇÃO** [(WEB ou BUSINESS)][(WEB ou BUSINESS)], selecione o **TAMANHO MÁXIMO** de seu banco de dados, escolha o **AGRUPAMENTO** e selecione **NOVO Servidor do Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo.

    ![Preencher as configurações do banco de dados SQL][Preencher as configurações do banco de dados SQL]

5.  Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo servidor de Banco de Dados SQL será criado e marque a caixa **Permitir que os serviços do Azure acessem o servidor**.

    ![Criar um novo servidor de banco de dados SQL][Criar um novo servidor de banco de dados SQL]

    Quando o site for criado, você verá o texto **Criação do site '[NOME DO SITE]' concluído com êxito**. Agora, você pode habilitar a publicação do Git.

6.  Clique no nome do site exibido na lista de sites para abrir o painel Início Rápido.

    ![Abrir o painel do site][Abrir o painel do site]

7.  Na parte inferior da página QuickStart, clique em **Configurar a publicação do Git**.

    ![Configurar a publicação do Git][Configurar a publicação do Git]

8.  Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

    ![Criar credenciais de publicação][Criar credenciais de publicação]

    Levará alguns segundos para configurar seu repositório.

9.  Quando o repositório estiver pronto, você verá instruções para enviar os arquivos do aplicativo por push ao repositório. Tome nota destas instruções - elas serão necessárias mais tarde.

    ![Instruções do Git][Instruções do Git]

## Obter informações da conexão do Banco de Dados SQL

Para conectar-se à instância do Banco de Dados SQL que está em execução nos Sites do Azure, você precisará das informações da conexão. Para obter informações sobre a conexão do Banco de Dados SQL, siga estas etapas:

1.  No Portal de Gerenciamento do Azure, clique em **RECURSOS VINCULADOS** e, em seguida, clique no nome do banco de dados.

    ![Recursos Vinculados][Recursos Vinculados]

2.  Clique em **Exibir Cadeias de Conexão**.

    ![Cadeia de conexão][Cadeia de conexão]

3.  Na seção **ODBC** da caixa de diálogo resultante, anote a cadeia de conexão uma vez que ela será usada posteriormente.

## Criar a tabela de tarefas

Para criar a tabela do banco de dados usada para armazenar itens para o aplicativo de lista de tarefas, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, selecione seu Banco de Dados SQL e, em seguida, clique em **GERENCIAR** na parte inferior da página. Se você receber uma mensagem informando que o IP atual não faz parte das regras de firewall, selecione **OK** para adicionar o endereço IP.

    ![botão gerenciar][botão gerenciar]

2.  Faça logon usando o nome e a senha de logon especificados ao criar o banco de dados anteriormente.

    ![logon para gerenciar o banco de dados][logon para gerenciar o banco de dados]

3.  Na parte inferior esquerda da página, selecione **Criar** e, em seguida, selecione **Nova Tabela**.

    ![Nova tabela][Nova tabela]

4.  Digite 'tarefas' como o **Nome da Tabela** e selecione **É Identidade?** para a coluna **ID**.

    ![nome da tabela definido para tarefas e é identidade verificada][nome da tabela definido para tarefas e é identidade verificada]

5.  Alterar a **Coluna1** para **nome** e **Coluna2** para **categoria**. Adicione duas novas colunas clicando no botão **Adicionar Coluna**. A primeira coluna nova deve ser nomeada **created** e ter um tipo de **date**. A segunda coluna nova deve ser nomeada **Concluído** e ter um tipo de **bit**. As duas novas colunas devem ser marcadas como **É Obrigatório?**.

    ![criação da tabela concluída][criação da tabela concluída]

6.  Clique no botão **Salvar** para salvar as alterações na tabela. Agora você pode fechar a página de gerenciamento do Banco de Dados SQL.

## Instalar módulos e gerar scaffolding

Nesta seção você criará um novo aplicativo de nó e usará o npm para adicionar pacotes de módulo. Para o aplicativo de lista de tarefas você usará os módulos [express][express] e [node-sqlserver][node-sqlserver]. O módulo Express fornece uma estrutura de Controlador de Exibição de Modelo para o nó, enquanto o módulo node-sqlserver fornece conectividade ao Banco de Dados SQL do Azure.

### Instalar o express e gerar scaffolding

1.  Da linha de comando, mude os diretórios para o diretório **tasklist**. Se o diretório **tasklist** não existir, crie-o.

2.  Insira o seguinte comando para instalar o express.

        npm install express -g

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Ao usar o par&acirc;metro '-g' em alguns sistemas operacionais, voc&ecirc; poder&aacute; receber uma mensagem de <strong>Erro: EPERM, chmod '/usr/local/bin/express'</strong> e uma solicita&ccedil;&atilde;o para tentar executar a conta como administrador. Se isso ocorrer, use o comando <strong>sudo</strong> para executar o npm com um n&iacute;vel de privil&eacute;gio mais elevado.</p>
</div>

    A saída desse comando deve ser semelhante ao seguinte:

        express@2.5.9 /usr/local/lib/node_modules/express
        ├── mime@1.2.4
        ├── mkdirp@0.3.0
        ├── qs@0.4.2
        └── connect@1.8.7

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>O par&acirc;metro '-g' usado durante a instala&ccedil;&atilde;o do m&oacute;dulo expresso o instala globalmente. Isso &eacute; feito para que possamos acessar o comando <strong>express</strong> para gerar o scaffolding do site sem precisar digitar informa&ccedil;&otilde;es adicionais de caminho.</p>
</div>

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

### Instalar módulos adicionais

1.  Na linha de comando, altere os diretórios para a pasta **tasklist** e digite o seguinte para instalar os módulos descritos no arquivo **package.json**:

        npm install

    A saída desse comando deve ser semelhante ao seguinte:

        express@2.5.8 ./node_modules/express
        ├── mime@1.2.4
        ├── qs@0.4.2
        ├── mkdirp@0.3.0
        └── connect@1.8.7
        jade@0.26.0 ./node_modules/jade
        ├── commander@0.5.2
        └── mkdirp@0.3.0

    Isso instala todos os módulos padrão de que o Express precisa.

2.  Em seguida, use o seguinte comando para adicionar o módulo nconf. Esse módulo será usado pelo aplicativo para ler a cadeia de conexão do banco de dados em um arquivo de configuração.

    npm instalar nconf -salvar

3.  Em seguida, baixe a versão binária do Driver da Microsoft para o Node.JS para SQL Server na [central de download][central de download].

4.  Extraia o arquivo morto para o diretório **tasklist\\node\_modules**.

5.  Execute o arquivo **msnodesql-install.cmd** no diretório **tasklist\\node\_modules**. Isso criará um subdiretório **msnodesql** em **node\_modules** e moverá os arquivos de driver para essa nova estrutura de diretórios.

6.  Exclua o arquivo **msnodesql-install.cmd**, pois ele não é mais necessário.

## Usar o Banco de Dados SQL em um aplicativo de nó

Nesta seção, você estenderá o aplicativo básico criado pelo comando **express** modificando o **app.js** existente e criará um novo arquivo **index.js** para usar o banco de dados criado anteriormente.

### Modificar o controlador

1.  No diretório **tasklist/routes**, abra o arquivo **index.js** em um editor de texto.

2.  Substitua o código existente no arquivo **index.js** pelo código a seguir. Isso carrega o msnodesql e os módulos nconf e usa o nconf para carregar a cadeia de conexão em uma variável de ambiente chamada **SQL\_CONN** ou em um valor de **SQL\_CONN** no arquivo **config.json**.

        var sql = require('msnodesql')
            , nconf = require('nconf');

        nconf.env()
             .file({ file: 'config.json' });
        var conn = nconf.get("SQL_CONN");

3.  Continue adicionando ao arquivo **index.js** adicionando os métodos **index** e **updateItem**. O método **index** retorna todas as tarefas não concluídas do banco de dados, enquanto **updateItem** irá marcar tarefas selecionadas como concluídas.

        exports.index = function(req, res) {
            var select = "select * from tasks where completed = 0";
            sql.query(conn, select, function(err, items) {
                if(err)
                    throw err;
                res.render('index', { title: 'My ToDo List ', tasks: items });
            });
        };

        exports.updateItem = function(req, res) {
            var item = req.body.item;
            if(item) {
                var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
                sql.query(conn, insert, [item.name, item.category], function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            } else {
                var completed = req.body.completed;
                if(!completed.forEach)
                    completed = [completed];
                var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                sql.query(conn, update, function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            }
        }

4.  Salvar o arquivo **index.js**.

### Modificar app.js

1.  No diretório **tasklist**, abra o arquivo **app.js** em um editor de texto. Este arquivo foi criado anteriormente usando o comando **express**.

2.  No arquivo app.js, role até ver o código abaixo.

        app.configure('development', function(){
        app.use(express.errorHandler());
        });

3.  Agora, insira o código a seguir.

        app.get('/', routes.index);
        app.post('/', routes.updateItem);

Isso adicionará uma nova rota ao método **updateItem** que você adicionou anteriormente ao arquivo **index.js**.

1.  Salve o arquivo **app.js**.

### Modificar a exibição de índice

1.  Altere os diretórios para o diretório **views** e abra o arquivo **index.jade** em um editor de texto.

2.  Substitua o conteúdo do arquivo **index.jade** pelo código abaixo. Isso define o modo de exibição das tarefas existentes, bem como um formulário para adicionar novas tarefas e marcar as tarefas existentes como concluídas.

        h1= title
        br

        form(action="/", method="post")
          table(class="table table-striped table-bordered")
            thead
              tr
                td Name
                td Category
                td Date
                td Complete
            tbody
            each task in tasks
              tr
                td #{task.name}
                td #{task.category}
                td #{task.created}
                td
                  input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
          button(type="submit", class="btn") Update tasks
        hr

        form(action="/", method="post", class="well")
          label Item Name:
          input(name="item[name]", type="textbox")
          label Item Category:
          input(name="item[category]", type="textbox")
          br
          button(type="submit", class="btn") Add Item

3.  Salve e feche o arquivo **index.jade**.

### Modificar o layout global

O arquivo **layout.jade** no diretório **views** é usado como um modelo global para outros arquivos **.jade**. Nesta etapa, você o modificará para usar a [Twitter Bootstrap][Twitter Bootstrap], que é um kit de ferramentas que facilita a criação de um site com uma aparência interessante.

1.  Baixe e extraia os arquivos para a [Twitter Bootstrap][1]. Copie o arquivo **bootstrap.min.css** da pasta **bootstrap\\css** para o diretório **public\\stylesheets** de seu aplicativo de lista de tarefas.

2.  Na pasta **views**, abra o **layout.jade** em seu editor de texto e substitua o conteúdo pelo seguinte:

        !!!html
        html
          head
            title= title
            meta(http-equiv='X-UA-Compatible', content='IE=10')
            link(rel='stylesheet', href='/stylesheets/style.css')
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
          body(class='app')
            div(class='navbar navbar-fixed-top')
              .navbar-inner
                .container
                  a(class='brand', href='/') My Tasks
            .container!= body

3.  Salve o arquivo **layout.jade**.

### Criar o arquivo de configuração

O arquivo **config.json** contém a cadeia de caracteres de conexão usada para se conectar ao Banco de Dados SQL e que é lida pelo arquivo **index.js** durante o tempo de execução. Execute as seguintes etapas para criar esse arquivo:

1.  No diretório **tasklist**, crie um novo arquivo chamado **config.json** e abra-o em um editor de texto.

2.  O conteúdo do arquivo **config.json** deve ser semelhante ao seguinte:

        {
          "SQL_CONN" : "connection_string"
        }

    Substitua a **connection\_string** pelo valor da cadeia de conexão ODBC retornada anteriormente.

3.  Salve o arquivo.

## Execute seu aplicativo localmente

Execute as etapas a seguir para testar o aplicativo em seu computador local.

1.  Da linha de comando, mude os diretórios para o diretório **tasklist**.

2.  Use o seguinte comando para iniciar o aplicativo localmente:

        node app.js

3.  Abra o navegador e acesse <http://127.0.0.1:3000>. Isso deve exibir uma página semelhante à seguinte:

    ![Uma página da Web que exibe uma lista de tarefas vazia][2]

4.  Use os campos fornecidos para **Nome do Item** e **Categoria do Item** para inserir informações e clique em **Adicionar item**.

5.  A página deverá ser atualizada para exibir o item na lista de Tarefas pendentes.

    ![Uma imagem do novo item na lista de tarefas][Uma imagem do novo item na lista de tarefas]

6.  Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir e clicar em **Atualizar tarefas**.

7.  Para parar o processo de nó, acesse a linha de comando e pressione as teclas **CTRL** e **C**.

## Implantar seu aplicativo no Azure

Nesta seção, você usará as etapas de implantação recebidas depois de criar o site para publicar seu aplicativo no Azure.

### Publicar o aplicativo

1.  Na linha de comando, altere os diretórios para o diretório **tasklist**, se você ainda não estiver nele.

2.  Use os seguintes comandos para inicializar um repositório git local para seu aplicativo, adicionar os arquivos do aplicativo ao diretório e, finalmente, enviar os arquivos para o Azure

        git init
        git add .
        git commit -m "adding files"
        git remote add azure [URL for remote repository]
        git push azure master

    No final da implantação, você verá uma instrução semelhante ao seguinte:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

3.  Quando a operação de envio por push for concluída, navegue até o **[http://[site][http://[site] name].azurewebsites.net/** para exibir o seu aplicativo.

### Alternar para uma variável de ambiente

Anteriormente, implementamos código que procura uma variável de ambiente **SQL\_CONN** para a cadeia de conexão ou carrega o valor do arquivo **config.json**. Nas etapas a seguir você criará um par de chaves/valor na configuração de seu site, que o aplicativo real acessa por meio de uma variável de ambiente.

1.  No Portal de Gerenciamento do Azure, clique em **Sites** e, em seguida, selecione seu site.

    ![Abrir o painel do site][Abrir o painel do site]

2.  Clique em **CONFIGURAR** e localize a seção **configurações do aplicativo** da página.

    ![configurar link][configurar link]

3.  Na seção **configurações do aplicativo**, digite **SQL\_CONN** no campo **CHAVE**, e a cadeia de conexão ODBC no campo **VALOR**. Finalmente, clique na marca de seleção.

    ![configurações do aplicativo][configurações do aplicativo]

4.  Finalmente, clique no ícone **SALVAR** na parte inferior da página para confirmar a alteração para o ambiente de tempo de execução.

    ![salvar as configurações do aplicativo][salvar as configurações do aplicativo]

5.  Na linha de comando, altere os diretórios para o diretório **tasklist** e digite o seguinte comando para remover o arquivo **config.json**:

        git rm config.json
        git commit -m "Removing config file"

6.  Execute o comando a seguir para implantar as alterações no Azure:

        git push azure master

Depois que as alterações tiverem sido implantadas no Azure, seu aplicativo da web deverá continuar a funcionar como agora, lendo a cadeia de conexão na entrada **configurações do aplicativo**. Para verificar isso, altere o valor da entrada **SQL\_CONN** em **configurações do aplicativo** para um valor inválido. Depois que você salvar esse valor, o site deverá falhar devido à cadeia de conexão inválida.

## Próximas etapas

-   [Aplicativo Web Node.js com MongoDB][Aplicativo Web Node.js com MongoDB]

-   [Aplicativo Web Node.js com Armazenamento de Tabela]

## Recursos adicionais

[Ferramenta de linha de comando do Azure para Mac e Linux] [Criar e implantar um aplicativo Node.js em sites do Azure]: /pt-br/develop/nodejs/tutorials/create-a-website-(mac)/
[Publicando Sites do Azure com Git][Publicando Sites do Azure com Git]: /pt-br/develop/nodejs/common-tasks/publishing-with-git/
[Azure Developer Center]: /pt-br/develop/nodejs/
[Aplicativo web Node.js com Armazenamento de tabela]: /pt-br/develop/nodejs/tutorials/web-site-with-storage/

  [nó]: http://nodejs.org
  [Git]: http://git-scm.com
  [Uma página da Web que exibe uma lista de tarefas vazia]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
  [Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/pt-br/download/details.aspx?id=29065
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Criar um novo site do Azure]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
  [Criação personalizada de um novo site]: ./media/sql-database-nodejs-use-web-site/custom_create.png
  [Preencha os detalhes do site]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
  [Preencher as configurações do banco de dados SQL]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
  [Criar um novo servidor de banco de dados SQL]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
  [Abrir o painel do site]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
  [Configurar a publicação do Git]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
  [Criar credenciais de publicação]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png
  [Instruções do Git]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
  [Recursos Vinculados]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
  [Cadeia de conexão]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
  [botão gerenciar]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
  [logon para gerenciar o banco de dados]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
  [Nova tabela]: ./media/sql-database-nodejs-use-web-site/new-table.png
  [nome da tabela definido para tarefas e é identidade verificada]: ./media/sql-database-nodejs-use-web-site/table-name-identity.png
  [criação da tabela concluída]: ./media/sql-database-nodejs-use-web-site/table-columns.png
  [express]: http://expressjs.com
  [node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
  [central de download]: http://www.microsoft.com/pt-br/download/details.aspx?id=29995
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [2]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
  [Uma imagem do novo item na lista de tarefas]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png
  [configurar link]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
  [configurações do aplicativo]: ./media/sql-database-nodejs-use-web-site/appsettings.png
  [salvar as configurações do aplicativo]: ./media/sql-database-nodejs-use-web-site/savebutton.png
  [Aplicativo Web Node.js com MongoDB]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
  [Publicando Sites do Azure com Git]: ../CommonTasks/publishing-with-git
