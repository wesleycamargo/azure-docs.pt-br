<properties 
	pageTitle="Compilar um aplicativo Web Node.js usando o Banco de Dados de Documentos | Azure" 
	description="Saiba como usar o Banco de Dados de Documentos do Microsoft Azure para armazenar e acessar dados de um aplicativo hospedado no Node.js no Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175">Criar um aplicativo Web Node.js usando o Banco de Dados de Documentos</a>

<a name="_Toc395783175">

Este tutorial mostra a você como usar o serviço do Banco de Dados de Documentos do Azure para armazenar e acessar dados
de um aplicativo Express do Node.js hospedado nos Sites do Azure.

Este tutorial supõe que você tenha alguma experiência prévia com o uso do Node.js.

Você aprenderá a:

· Usar as Ferramentas do Node.js para Visual Studio

· Trabalhar com o serviço do Banco de Dados de Documentos do Azure usando o módulo npm do Banco de Dados de Documentos

· Implantar o aplicativo Web nos sites do Azure

Seguindo este tutorial, você criará um aplicativo simples de gerenciamento de tarefas
baseado na web que permite criar, recuperar e
concluir tarefas. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos do
Azure.

![Texto Alt][Texto Alt]

## <a name="_Toc395783176">Pré-requisitos</a>

Antes de seguir as instruções deste artigo, verifique se
você possui os seguintes itens instalados:

[Node.js][Node.js] versão v0.10.29 ou superior

[Git][Git]

[Visual Studio 2013][Visual Studio 2013] com atualização 3

[Ferramentas do Node.js para Visual Studio][Ferramentas do Node.js para Visual Studio]

**Observação:** embora estejamos usando o Visual Studio para criar, depurar e implantar nosso projeto Node.js
neste tutorial, você pode usar qualquer editor de sua preferência
e executar o Node.js diretamente em qualquer plataforma da forma que você
normalmente executaria em projeto Node.js. Em seguida, você pode usar as ferramentas do [Azure CLI][Azure CLI] para
implantar seu aplicativo nos Sites do Azure

# <a name="_Toc395637761">Criar uma conta de banco de dados do Banco de Dados de Documentos</a>

Para provisionar uma conta de Banco de Dados de Documentos no Azure, abra o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]
e clique no bloco da Galeria do Azure na página inicial ou clique em “+” no canto inferior esquerdo da tela.

![Texto Alt][1]

Isso abrirá a Galeria do Azure, onde você pode selecionar por meio de muitos serviços
disponíveis do Azure. Na Galeria, selecione “Dados, armazenamento e
backup” na lista de categorias.

![Texto Alt][2]

A partir daqui, selecione a opção para Banco de Dados de Documentos do Azure

![Texto Alt][3]

Depois, selecione “Criar” na parte inferior da tela

![Texto Alt][4]

Isso abrirá a blade “Novo Banco de Dados de Documentos”, onde você pode especificar o
nome, a região, a escala, o grupo de recursos e outras configurações para sua nova
conta.

![Texto Alt][5]

Depois de concluir a inserção de valores na conta, clique em “Criar”,
e o processo de provisionamento começará a criar sua conta de banco de dados.
Quando o provisionamento estiver concluído, você verá uma notificação
exibida na área de notificações do portal, e o bloco na sua
tela inicial (se você tiver selecionado para criar uma tela) mudará para mostrar a
ação concluída.

![Texto Alt][6]

Depois de concluir o provisionamento, clique no bloco do Banco de Dados de Documentos na
tela inicial para exibir o blade principal dessa conta de Banco de Dados de Documentos
recém-criada.

![Texto Alt][7]
![Texto Alt][8]

Usando o botão “Chaves”, acesse a URL do ponto de extremidade e a Chave Primária,
copie-as para sua área de transferência e mantenha-as por perto, pois usaremos esses
valores no aplicativo Web que criaremos em seguida.

## <a name="_Toc395783178">Criar um novo aplicativo Node.js</a>

No Visual Studio, selecione Arquivo – Novo Projeto e selecione a opção para criar um “Aplicativo Express básico do Microsoft Azure”

![Texto Alt][9]

Isso criará um aplicativo Express básico para você. Se você for
solicitado a “instalar dependências”, selecione “Sim”. Isso instalará todos os
pacotes npm necessários para um novo aplicativo Express.

Feito isso, o Gerenciador de Soluções deve se parecer com o
seguinte;

![Texto Alt][10]

Isso mostra que você tem Express, Jade e Stylus instalados.

Se você pressionar F5 no Visual Studio, ele deve criar o projeto, iniciar o
Node.js e exibir um navegador com o equivalente do Express de “Olá
mundo”

![Texto Alt][11]

## <a name="_Toc395783179">Instalar módulos adicionais</a>

O arquivo **package.json** está em um dos arquivos criados na raiz do
projeto. Este arquivo contém uma lista dos módulos adicionais que são
necessários para um aplicativo Express. Posteriormente, ao implantar esse
aplicativo em um site do Azure, esse arquivo será usado para determinar
quais módulos precisam ser instalados no Azure para oferecer suporte ao seu aplicativo.

![Texto Alt][12]

Precisamos instalar mais dois pacotes para este tutorial.

Clique com o botão direito em “npm” no Gerenciador de Soluções e selecione “Instalar Pacotes
npm”

![Texto Alt][13]

Na caixa de diálogo “Instalar Novos Pacotes npm”, digite **nconf** para procurar
o módulo. Esse módulo será usado pelo aplicativo para ler os
valores de configuração do banco de dados em um arquivo de configuração.

![Texto Alt][14]

Por fim, instale o módulo do Banco de Dados de Documentos do Azure da mesma maneira procurando
por **documentdb**. Esse é o módulo onde toada a mágica do Banco de Dados de Documentos
acontece.

Depois que você instalar esses dois módulos adicionais, e as dependências,
eles deverão aparecer no Gerenciador de Soluções nos módulos **npm**
.

![Texto Alt][15]

Uma rápida verificação do arquivo **package.json** do aplicativo deve
mostrar os módulos adicionais. Esse arquivo informará ao Azure mais tarde quais
pacotes ele precisa que sejam baixados e instalados ao executar seu
aplicativo.

Edite o arquivo package.json, se necessário, para ficar semelhante ao exemplo abaixo.

![Texto Alt][16]

Isso informa ao Nó (e ao Azure mais tarde) que seu aplicativo depende desses módulos
adicionais.

# <a name="_Toc395783180">Usando o serviço do Banco de Dados de Documento em um aplicativo de nó</a>

Isso cuida de toda a instalação e configuração inicial. Agora vamos ao motivo de estarmos aqui, ou seja, para gravar algum código usando o Banco de Dados de Documentos do Azure. Para iniciar, edite **app.js** localizado na raiz do aplicativo Express que acabamos de criar. Localize as seguintes linhas no arquivo;

    app.get('/', routes.index);
    app.get('/users', user.list);

E substitua com as seguintes duas linhas;

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Isso informa ao aplicativo o que fazer com os métodos GET e POST padrão em um Formulário que criaremos em seguida.

Agora localize o arquivo **index.js** na pasta **roteadores**. Abra-a no seu editor e exclua todo código encontrado nesse arquivo.

Adicione o seguinte na parte superior do arquivo;

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Isso define os módulos que vamos usar, que são **documentdb** e **nconf**.

**nconf** é um módulo que permite carregar valores de configuração, como cadeias de conexão do banco de dados, por meio de arquivos externos, em vez de colocar esses valores em linha no seu código. nconf buscará um **config.json** por padrão para sua configuração.

Vamos prosseguir e criar um arquivo de texto vazio chamado **config.json** na raiz do projeto (mesmo local do app.js)

Abra esse novo arquivo **config.json** e insira os seguintes valores conforme adequado no ponto de extremidade do Banco de Dados de Documentos. Configure os valores HOST e MASTER\_KEY corretamente.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Agora, voltando ao **index.js**, adicione as seguintes linhas após as últimas linhas para realmente ler o arquivo de configuração e armazenar os valores nas variáveis do nível da página.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

Agora que isso foi feito, continue adicionando o seguinte código ao **index.js**

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Essas são as duas funções que informamos ao aplicativo para usar anteriormente no **app.js** quando definimos as rotas. Quando um GET chega à exibição do índice, a função **exports.index** será executada e, de modo semelhante, quando um POST é recebido pela exibição do índice, a função **exports.createOfUpdateItem** será executada.

Essas duas funções fazem todo o trabalho do aplicativo que estamos criando. Porém, elas chamam outras funções, basicamente para deixar o código mais legível e fácil de seguir. Continue adicionando o seguinte código ao arquivo **index.js**. Este contém todos os métodos usados pelas duas funções acima e contém todas as chamadas ao Banco de Dados de Documentos. Vamos abordar cada função mais detalhadamente adiante.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** – atualiza um documento no banco de dados com base na ID do Item transferida do formulário. Ela usa essa ID para executar um método ***readDocument*** no Banco de Dados de Documentos para ler o documento específico que armazenamos. Em seguida, ela muda o atributo “completed” do documento para verdadeiro, indicando que agora ele está concluído e continua a substituição do documento no banco de dados.

**getItem –** usa ***queryDocuments*** para obter um único item do banco de dados usando a propriedade de ID do item.

**createItem** – usa método ***createDocument*** para criar um novo documento no banco de dados com base no Nome do Item e na Categoria Item inserida no formulário. Ela também configura a marcação de concluído para falso, para indicar que esse item ainda não foi concluído.

**listItems** – usa ***queryDocuments*** para procurar todos os documentos na coleção que ainda não estão concluídos ou foram concluídos=falso. Ela usa a gramática da consulta do Banco de Dados de Documentos que é baseada no ANSI-SQL para demonstrar este recurso de consulta familiar, porém poderoso.

**readOrCreateDatabase** – usa ***queryDatabases*** para verificar se um banco de dados com esse nome já existe. Se não conseguirmos encontrar um, prosseguimos e usamos ***createDatabase*** para criar um novo banco de dados com o identificador fornecido (do nosso arquivo de configuração) no ponto de extremidade especificado (também do arquivo de configuração)

**readOrCreateCollection** – assim como o readOrCreateDatabase, este método primeiro tenta encontrar uma coleção com o identificador fornecido, caso exista um, ele é retornado e, se não existir, ele é criado para você.

Esse é todo o código necessário para interagir com o Banco de Dados de Documentos para este aplicativo de exemplo.

Agora vamos voltar a atenção para criar a interface do usuário, desse modo, um usuário pode realmente interagir com nosso aplicativo. O aplicativo Express que criamos usa **Jade** como mecanismo de exibição. Para obter mais informações sobre o Jade, consulte <http://jade-lang.com/>

Abra o arquivo **layout.jade** encontrado na pasta **exibições** e substitua o conteúdo pelo seguinte;

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Isso informa efetivamente ao mecanismo **Jade** para renderizar algum HTML para nosso aplicativo e criar um “**bloco**” chamado “**conteúdo**”, onde podemos fornecer o layout para nossas páginas de conteúdo.

Agora abra o arquivo **index.jade**, a exibição associada ao index.js e substitua o conteúdo pelo seguinte;

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Isso estende o layout e fornece conteúdo para o espaço reservado “block content”
que vimos no arquivo layout.jade anteriormente.

Nesse layout criamos dois formulários HTML. O primeiro formulário contém uma tabela
de nossos dados e um botão que permite atualizar itens. O segundo formulário contém
alguns campos de entrada e um botão que permite criar um novo item.

Quando clicamos em algum desses botões, o **createOrUpdateItem**
(ou seja, um formulário POST é feito) e a função que criamos no arquivo **index.js**
será chamada. Se essa página for carregada diretamente (ou seja, um GET for
realizado), a função **index** será executada.

Isso deve ser tudo o que precisamos para que nosso aplicativo funcione.

</h1>
# <a name="_Toc395783181">Execute seu aplicativo localmente</a>

Para testar o aplicativo no seu computador local, pressione F5 no Visual Studio
e ele deve compilar o aplicativo, iniciar o Node.js e um navegador com uma
página que se parece com a imagem abaixo:

![Texto Alt][17]

1. Use os campos fornecidos para Item, Nome do Item e Categoria para inserir
informações e clique em **Adicionar Item**.

2. A página deverá ser atualizada para exibir o item recém-criado na lista de
Tarefas Pendentes.

	![Texto Alt][18]

3. Para concluir uma tarefa, basta marcar a caixa de seleção na coluna Concluir
e clicar em **Atualizar tarefas**

</h1>
# <a name="_Toc395783182">Implantar o aplicativo no Azure</a>

Com as Ferramentas do Node.js para Visual Studio instaladas, é fácil implantar os Sites do
Azure em poucas etapas breves.

Clique com o botão direito do mouse no seu projeto e selecione “Publicar”

![Texto Alt][19]

Depois, siga o assistente de publicação para fornecer a configuração necessária para o
Site do Azure. O assistente deixará você escolher um site
existente para ser atualizado ou criar um novo site.

Depois de fornecer a configuração necessária, basta clicar em “Publicar”

![Texto Alt][20]

Depois, siga o assistente de publicação para fornecer a configuração necessária para o
Site do Azure. O assistente deixará você escolher um site
existente para ser atualizado ou criar um novo site.

Depois de fornecer a configuração necessária, basta clicar em “Publicar”

E o Visual Studio conectará a sua assinatura do Azure e publicará
esse aplicativo do Node.js.

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo
Web e iniciará um navegador onde você poderá ver seu trabalho
sendo executado no Azure!

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Conclusão</a>

Parabéns! Você acabou de criar seu primeiro Aplicativo
Web Express do Node.js usando o Banco de Dados de Documentos do Azure e publicá-lo nos sites do Azure.

O código-fonte para o aplicativo de referência completo pode ser baixado [aqui][aqui].

</h1>

  [Texto Alt]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/vstudio/cc136611.aspx
  [Ferramentas do Node.js para Visual Studio]: https://nodejstools.codeplex.com/
  [Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
  [Portal de Gerenciamento do Azure]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [aqui]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409

<!--HONumber=46--> 
