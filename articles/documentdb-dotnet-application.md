<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Compilar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos | Azure" description="Saiba como usar o Banco de Dados de Documentos para criar um aplicativo Web de lista de tarefas pendentes. Você vai armazenar e acessar dados por meio de um aplicativo Web ASP.NET MVC hospedado no Azure."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Criar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Visão geral</a>
==============================================================================================

<a name="_Toc395637759">Cenário</a>
------------------------------------

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para armazenar e consultar documentos JSON, este documento fornece um passo a passo completo sobre como compilar um aplicativo Web de Lista de tarefas pendentes usando o Banco de Dados de Documentos do Azure.

Este passo a passo mostra como usar o serviço Banco de Dados de Documentos fornecido pelo Azure para armazenar e acessar dados por meio de um aplicativo Web ASP.NET MVC hospedado no Azure e supõe que você tenha experiência prévia com o uso do ASP.NET MVC e os Sites do Azure.

Você aprenderá a:

1\. Criar e provisionar uma Conta de Banco de Dados de Documentos

2\. Criar um aplicativo ASP.NET MVC

3\. Conectar-se a e usar o Banco de Dados de Documento do Azure a partir do aplicativo Web

4\. Implantar o aplicativo Web nos sites do Azure

Seguindo este passo a passo, você compilará um aplicativo simples de gerenciamento de tarefas baseado na Web que permite criar, recuperar e concluir as tarefas. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos do Azure.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

Git para Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (ou [Visual Studio Express][], que é a versão gratuita)

SDK do Azure para .NET versão 2.3 ou superior, disponível pelo [Microsoft Web Platform Installer][]

Todas as capturas de tela neste documento foram feitas usando o Visual Studio 2013 com Atualização 3 aplicada e o SDK do Azure para .NET versão 2.4. Se o seu sistema estiver configurado com versões diferentes, será possível que suas telas e opções não correspondam totalmente, mas se você cumprir os pré-requisitos acima, esta solução deverá funcionar.

<a name="_Toc395637761">Criar uma conta de banco de dados do Banco de Dados de Documentos</a>
================================================================

Para provisionar uma conta de Banco de Dados de Documentos no Azure, abra o Portal de Gerenciamento do Azure e clique no bloco Galeria do Azure na home page ou em "+" no canto inferior esquerdo da tela.

![Alt text](./media/documentdb-dotnet-application/image2.png)


Isso abrirá a Galeria do Azure, onde você pode selecionar entre os muitos serviços disponíveis do Azure. Na Galeria, selecione "Dados, armazenamento e backup" na lista de categorias.

![Alt text](./media/documentdb-dotnet-application/image3.png)

A partir daqui, selecione a opção para Banco de Dados de Documentos do Azure

![Alt text](./media/documentdb-dotnet-application/image4.png)


Depois selecione "Criar" na parte inferior da tela

![Alt text](./media/documentdb-dotnet-application/image5.png)

Isso abrirá a folha "Novo Banco de Dados de Documentos", onde você pode especificar o nome, a região, a escala, o grupo de recursos e outras configurações para sua nova conta.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Depois de concluir a inserção de valores na conta, clique em "Criar" e o processo de provisionamento começará a criar sua conta de banco de dados.
Quando o processo de provisionamento estiver concluído, você verá uma notificação exibida na área de notificações do portal, e o bloco na sua tela inicial (se você tiver escolhido criar um) mudará para mostrar a ação concluída.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Depois de concluir o provisionamento, clicar no bloco Banco de Dados de Documentos na tela inicial exibirá a folha principal dessa conta de Banco de Dados de Documentos recém-criada.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Usando o botão "Chaves", acesse a URL do ponto de extremidade e a Chave Primária, copie-as para sua área de transferência e mantenha-as por perto, pois usaremos esses valores no aplicativo Web que será criado em seguida.

Agora vamos abordar como criar um novo aplicativo ASP.NET MVC desde o início. Para sua referência, a solução completa pode ser baixada [aqui].

<a name="_Toc395637762">Criar um novo aplicativo ASP.NET MVC</a>
================================================================

No Visual Studio, clique em Arquivo - Novo Projeto e selecione a opção para criar um novo aplicativo Web ASP.NET MVC.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Selecione onde você deseja criar o projeto, como deseja que o projeto seja chamado (Estou usando "todo") e clique em Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


Se você planeja hospedar seu aplicativo no Azure, marque a caixa na parte inferior direita para "Hospedar na nuvem". Selecionamos para hospedar na nuvem e executar o aplicativo hospedado no site do Azure. Selecionar essa opção provisiona previamente um Site do Azure para você e torna mais fácil a implantação do aplicativo de trabalho final. Se desejar hospedá-lo em outro local ou se não desejar configurar o Azure com antecedência, apenas desmarque a opção "Hospedar na Nuvem".

Selecione OK e deixe o Visual Studio fazer isso realizando scaffolding do modelo ASP.NET MVC vazio. Se você optar por hospedá-lo na Nuvem, verá pelo menos uma tela adicional pedindo para você fazer logon na conta do Azure e fornecer alguns valores para o novo site. Prossiga para fornecer todos esses valores do Azure e continue. 

Não escolhi "Servidor de banco de dados" aqui porque não estamos usando o Servidor do Banco de Dados SQL do Azure; vamos criar uma nova Conta de Banco de Dados de Documentos do Azure posteriormente no portal de gerenciamento. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Depois que o Visual Studio concluir a criação do aplicativo MVC de texto clichê, você terá um aplicativo ASP.NET vazio que poderá ser executado localmente.

Vamos ignorar a execução local porque tenho certeza de que vimos o aplicativo "Hello World" do ASP.NET. Vamos passar direto para a adição do Banco de Dados de Documentos a este projeto e a criação de nosso aplicativo.

</h1>
<a name="_Toc395637767">Adicionando o Banco de Dados de Documentos ao seu projeto</a>
=============================================================

Isso cuida da maioria das conexões ASP.NET MVC necessárias para essa solução. Agora vamos abordar a finalidade real deste tutorial, adicionando o Banco de Dados de Documentos do Azure ao seu aplicativo Web.

### 

### <a name="_Toc395637764">Instalando o pacote NuGet</a>

O SDK .NET do Banco de Dados de Documentos é distribuído como um pacote NuGet. Use o gerenciador do pacote NuGet no Visual Studio (que você pode acessar clicando com o botão direito do mouse no Projeto e escolhendo "Gerenciar Pacotes NuGet").

![Alt text](./media/documentdb-dotnet-application/image21.png)

Procure online pelo "Banco de Dados de Documentos do Azure" e instale o pacote. Essa ação baixará e instalará o pacote do Banco de Dados de Documentos, bem como todas as dependências, como Newtonsoft.Json.

**OBSERVAÇÃO:** enquanto o serviço ainda estiver em visualização, o pacote NuGet ficará marcado como "Pré-lançamento". Por isso, você precisará incluir a opção "Incluir Pré-lançamento" ou não encontrará o pacote. 

Se preferir, poderá usar o Console de Comando do Pacote para instalar o pacote digitando:

    Install-Package Microsoft.Azure.Documents.Client -Pre

Depois de instalada, sua solução do Visual Studio deverá se parecer com a imagem a seguir, com duas novas referências adicionadas;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Configurando o aplicativo ASP.NET MVC</a>
==================================================================

### 

### <a name="_Toc395637764">Adicionar um modelo</a>

Vamos começar criando o **M** no MVC, o modelo. No Gerenciador de Soluções, clique com o botão direito do mouse na *Models* pasta e em **Adicionar**. Em seguida, clique em **Classe**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Nomeie a nova Classe, **Item**, e adicione o seguinte código a essa nova Classe

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Todos os dados no Banco de Dados de Documentos são transferidos e armazenados como JSON. Para controlar a forma como seus objetos são serializados/desserializados pelo JSON.NET, você pode usar o atributo JsonProperty, como demonstrado na classe Item criada há pouco. Você não **obrigado** a fazer isso, mas quero garantir que minhas propriedades seguiram as convenções de nomenclatura camelCase de JSON. 

Além de poder controlar o formato do nome da propriedade quando ele entra no JSON, você também pode renomear totalmente as propriedades .NET, como fiz com a propriedade Descrição. 

Se desejar, você também pode usar objetos JsonConverter aqui para controlar totalmente como a serialização é manipulada.  

Para que o Visual Studio resolva o atributo "JsonProperty" usado aqui, é preciso adicionar a instrução using a seguir na seção using de seu arquivo de classe;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Adicionar um controlador</a>

Isso cuida do M. Agora, vamos criar o **C** no MVC, uma classe de controlador.
No **Gerenciador de Soluções**, clique com o botão direito do mouse na *Controllers* pasta e em **Adicionar**. Em seguida, clique em **Controlador**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


Na caixa de diálogo **Adicionar Scaffold**, clique em **Controlador MVC 5 - Vazio.** Clique em **Adicionar**.

![Alt text](./media/documentdb-dotnet-application/image15.png)

Nomeie o novo Controlador, **ItemController.**

Agora, o Visual Studio adicionará o ItemController. O Gerenciador de Soluções deve ser semelhante ao seguinte:

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Adicionar exibições</a>

E, por fim, vamos criar o **V** no MVC, uma exibição.


#### Adicionar exibição para índice de itens

Expanda a pasta ***Exibições*** no Gerenciador de Soluções e encontre a pasta Item (vazia) que o Visual Studio criou quando você adicionou o *ItemController* anteriormente. Clique com o botão direito do mouse em ***Item*** e escolha Adicionar uma nova Exibição.

![Alt text](./media/documentdb-dotnet-application/image17.png)

Na caixa de diálogo "Adicionar Exibição". Chame sua exibição de "***Índice***", use o ***Modelo Lista***, selecione ***Item (todo.Models)*** que criamos anteriormente como a classe e, por fim, use ***~Views/Shared/_Layout.cshtml*** na Solução como a página de Layout.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Depois de definir todos esses valores, clique em Adicionar e deixe o Visual Studio criar sua exibição para você. O Visual Studio criará uma exibição do modelo. Feito isso, ele abrirá o arquivo cshtml criado. Podemos fechar esse documento no Visual Studio, pois voltaremos a ele mais tarde.

#### Adicionar exibição para novo item

De maneira semelhante ao que foi mostrado acima, crie uma nova Exibição a fim de criar novos Itens, conforme o exemplo mostrado abaixo;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Adicionar exibição para editar item

<a name="_Toc395888515"></a>
============================

E, por fim, adicione uma última Exibição para editar um Item da mesma maneira que foi feito antes;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Feito isso, feche os documentos cshtml no Visual Studio, pois voltaremos a essas Exibições mais tarde.

</h1>
<a name="_Toc395637769">Conectando o Banco de Dados de Documentos</a>
------------------------------------------------

### <a name="_Toc395637770">Listando itens incompletos</a>

Abra o **ItemController** e remova todo o código dentro da classe (mas deixe a classe) que o Visual Studio adicionou. Vamos recompilá-lo parte por parte usando o Banco de Dados de Documentos.

Adicione o seguinte trecho de código à classe ItemController que agora está vazia;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Esse código também usa uma classe de "pseudorrepositório" chamada DocumentDBRepository, que ainda temos que criar. Na verdade, isso é apenas uma classe Auxiliar que contém todos os códigos específicos do Banco de Dados de Documentos. Para as finalidades deste passo a passo, não vamos implementar uma camada completa de acesso a dados com injeção de dependência, fábricas e padrões de repositório, como você provavelmente faria se estivesse criando um aplicativo real. 
Para as finalidades deste passo a passo, vamos apenas colocar toda a lógica de acesso a dados diretamente em um projeto para manter a simplicidade e o foco nos bits específicos do Banco de Dados de Documentos.

Adicione uma nova classe ao seu projeto e chame-a de **DocumentDBRepository.**
Substitua o código no arquivo da classe pelo seguinte:

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Reserve um tempo para resolver todos os namespaces no Visual Studio. Todos os namespaces serão fáceis de resolver no Visual Studio desde que o pacote NuGet tenha sido instalado com êxito. As referências aos métodos ReadOrCreateDatabase e ReadOrCreateCollection continuarão sem solução até que eles sejam adicionados, o que faremos em seguida. 

Há duas chamadas de método usadas aqui para leitura ou criação de Bancos de Dados do Banco de Dados de Documentos e de Coleções de Documentos.
Adicione os dois métodos a seguir a essa classe;

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

Isso toma conta da instalação do Banco de Dados, um DocumentCollections, e da criação de alguns códigos para se conectar ao Banco de Dados de Documentos por meio de DocumentClient. 

Estamos lendo alguns valores por meio da configuração, por isso abra **web.config** e adicione as linhas a seguir sob a seção
<AppSettings\>;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Agora vamos adicionar alguns códigos para fazer o trabalho. 
A primeira coisa que desejamos fazer com um aplicativo de lista de tarefas é exibir os itens incompletos. O método a seguir faz isso para você, então copie e cole-o em qualquer lugar dentro da classe de repositório com a qual estamos trabalhando;
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
A referência a *CreateDocumentQuery* será resolvida depois; você precisa adicionar manualmente o seguinte usando a diretiva;

    using Microsoft.Azure.Documents.Linq;

Neste ponto, sua solução deve ser capaz de compilar sem erros.

Se você executou o aplicativo agora, deve ir para o Controlador Inicial e a exibição Índice desse controlador. Esse é o comportamento padrão para o projeto do modelo MVC que escolhemos no início, mas não queremos isso! Vamos alterar o roteamento neste aplicativo MVC para alterar seu comportamento.

Abra ***App\_Start\RouteConfig.cs*** e localize a linha que começa com "defaults:" e altere-a para que se pareça com o seguinte;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Agora isso informa ao ASP.NET MVC que se você não especificou um valor na URL para controlar o comportamento de roteamento que, em vez de "Home", usa "Item" como controlador e o usuário Índice como exibição.
Agora, se você executar o aplicativo, ele será chamado para seu **ItemController** e retornará os resultados do método **GetIncompleteItems** para a exibição Views\Item\Index. 

Se você compilar e executar esse projeto agora, deverá ver algo parecido com isto;    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Adicionando itens</a>

Vamos colocar alguns itens em nosso banco de dados; assim, temos algo mais que uma grade vazia para observar.

Já temos uma Exibição para Criar, e um Botão na Exibição de Índice que levará o usuário para a exibição de criação. Vamos adicionar algum código ao Controlador e ao Repositório para persistir no registro no Banco de Dados de Documentos.

Abra o ***ItemController.cs*** e adicione o trecho de código a seguir, por meio do qual o ASP.NET MVC sabe o que fazer para a ação Criar; neste caso, basta renderizar a exibição Create.cshtml associada criada anteriormente.

    public ActionResult Create()
    { 
	return View(); 
    }

Agora precisamos de mais algum código nesse controlador que aceitará o envio por meio da exibição de criação.

Adicione o próximo bloco de código que diz ao ASP.NET MVC o que fazer com um formulário POST para esse controlador.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Observação de segurança**: O atributo [ValidateAntiForgeryToken] é usado aqui para ajudar a proteger esse aplicativo contra ataques de solicitação intersite forjada. Há mais do que apenas adicionar esse atributo, as exibições precisam trabalhar com esse token antifalsificação também. Para saber mais sobre o assunto e ver exemplos de como implementar isso corretamente, consulte [Preventing Cross-Site Request Forgery (Prevenindo solicitação intersite forjada)][]. O código-fonte no link de download no final do artigo possui a implementação completa em vigor.

**Observação de segurança**: Também usamos o atributo [Bind] no parâmetro de método para ajudar na proteção contra ataques de overposting. Para obter mais detalhes, consulte [Basic CRUD Operations in ASP.NET MVC (Operações CRUD básicas no ASP.NET MVC)][]

Agora que isso está em vigor, o Controlador de Itens transmitirá (com segurança) o Item, do formulário para o método CreateDocument do repositório da classe. Portanto, adicione o método a seguir à classe DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Este método simplesmente pega algum objeto passado para ele e persiste-o no Banco de Dados de Documentos.
Isso conclui o código exigido para adicionar novos itens ao nosso banco de dados.


### <a name="_Toc395637772">Editando itens</a>

Existe uma última ação para realizarmos, e esta é a capacidade de editar itens no banco de dados e marcá-los como concluídos à medida que concluímos as tarefas. Assim como na adição, a exibição para edição já foi adicionada, desse modo, basta adicionar algum código adicional ao nosso Controlador e à classe DocumentDBRepository novamente.

Adicione o seguinte à classe ItemController;

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

O primeiro método lida com o Http Get que ocorrerá quando o usuário clicar no link "Editar" na exibição Índice. Esse método busca um Documento do Banco de Dados de Documentos e o transfere para a exibição Editar.

Em seguida, a exibição Editar fará um Http Post para o IndexController. 
O segundo método que adicionamos lida com isso transferindo objeto atualizado por meio do Banco de Dados de Documentos para que seja persistido no banco de dados.

Adicione o seguinte à classe DocumentDBRepository;

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

O primeiro desses dois métodos busca um Item do Banco de Dados de Documentos que é transferido de volta para o ItemController e, em seguida, para a exibição Editar.

O segundo dos dois métodos que acabamos de adicionar substitui o documento no Banco de Dados de Documentos pela versão do documento transferido do ItemController.

Ou seja, isso é tudo que precisamos para executar nosso aplicativo, Listar itens incompletos, Adicionar novos itens e, por fim, Editar itens.

</h3>
<a name="_Toc395637773">Execute seu aplicativo localmente</a>
=========================================================

### 

Para testar o aplicativo no seu computador local, pressione F5 no Visual Studio e ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos anteriormente:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Use os campos fornecidos para Item, Nome do Item e Categoria para inserir informações, clique no link **"Criar Novo"** e forneça alguns valores. Deixe a caixa de seleção "Concluído" desmarcada, caso contrário, o novo item será adicionado em um estado concluído e não aparecerá na lista inicial.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Se clicar em Criar, você será redirecionado de volta para a página Índice e espera-se que seu Item seja mostrado na Lista.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Fique à vontade para adicionar mais alguns itens à sua lista de Tarefas Pendentes.

2\.Clique em "Editar" perto de um Item na lista e você será levado para a exibição Editar, onde poderá atualizar qualquer propriedade do objeto, incluindo o sinalizador "Concluído". Isso marca efetivamente o item como concluído e o removerá da Lista de tarefas incompletas.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Para concluir uma tarefa, basta marcar a caixa de seleção e clicar em **Salvar.** Você será redirecionado de volta para a página de lista onde agora o item não aparecerá mais na lista.

</h3>
<a name="_Toc395637774">Implante o aplicativo nos sites do Azure</a>
================================================================

### 

Agora que você possui o aplicativo completo funcionando corretamente no Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure.

Se você selecionou "Hospedar na nuvem" quando criamos o projeto ASP.NET MVC vazio, o Visual Studio tornará essa ação muito fácil e fará a maior parte do trabalho para nós. Para Publicar este aplicativo, basta clicar com o botão direito do mouse no Projeto no Gerenciador de Soluções (certifique-se de que você ainda não o esteja executando localmente) e selecionar Publicar

![Alt text](./media/documentdb-dotnet-application/image28.png)

Tudo já deve estar configurado de acordo com suas credenciais; na verdade, o site já foi criado no Azure para você na "URL de Destino" mostrada, basta clicar em **Publicar**

![Alt text](./media/documentdb-dotnet-application/image29.png)

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

</h3>



<a name="_Toc395637775">Conclusão</a>
======================================

### 

Parabéns! Você acabou de criar seu primeiro Aplicativo ASP.NET MVC usando o Banco de Dados de Documentos do Azure e publicá-lo nos sites do Azure. O código-fonte para o aplicativo completo, incluindo a funcionalidade Detalhes e Exclusão ignorada aqui, pode ser baixado [aqui][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/pt-br/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[aqui]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Preventing Cross-Site Request Forgery (Prevenindo Solicitação Intersite Forjada)]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC (Operações CRUD Básicas no ASP.NET MVC)]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->
