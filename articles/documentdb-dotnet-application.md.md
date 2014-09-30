<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">Criar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Visão geral</a>

## <a name="_Toc395637759">Cenário</a>

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para
armazenar e consultar documentos JSON, este documento fornece um passo a passo
completo sobre como criar um aplicativo Web de Lista de tarefas pendentes usando o Banco de Dados de Documentos do Azure
.

Este passo a passo mostra como usar o serviço Banco de Dados de Documentos fornecido pelo
Azure para armazenar e acessar dados de um aplicativo Web ASP.NET MVC
hospedado no Azure e supõe que você tenha experiência prévia com o uso do
ASP.NET MVC e os sites do Azure.

Você aprenderá a:

1. Criar e provisionar uma Conta de Banco de Dados de Documentos

2. Criar um aplicativo ASP.NET MVC

3. Conectar-se a e usar o Banco de Dados de Documento do Azure a partir do aplicativo Web

4. Implantar o aplicativo Web nos sites do Azure

Seguindo esse passo a passo, você criará um aplicativo simples de gerenciamento de tarefas
baseado na Web que permite criar, recuperar e
concluir as tarefas. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos do
Azure.

![Texto Alt][]

# <a name="_Toc395637760">Pré-requisitos</a>

Antes de seguir as instruções deste artigo, verifique se
você possui os seguintes itens instalados:

Git para Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (ou [Visual Studio Express][], que é a
versão gratuita)

SDK do Azure para .NET versão 2.3 ou superior, disponível pelo
[Microsoft Web Platform Installer][]

Todas as capturas de tela neste documento foram feitas usando o
Visual Studio 2013 com Atualização 3 aplicada e o SDK do Azure para .NET versão
2.4. Se o seu sistema está configurado com versões diferentes, é provável
que suas telas e opções não correspondam totalmente, mas se você cumprir os
pré-requisitos acima, esta solução deve funcionar.

# <a name="_Toc395637761">Criar uma conta de banco de dados do Banco de Dados de Documentos</a>

Para provisionar uma conta de Banco de Dados de Documentos no Azure, abra o Portal de Gerenciamento do Azure
e clique no bloco Galeria do Azure na
home page ou clique em "+" no canto inferior esquerdo da tela.

![Texto Alt][1]

Isso abrirá a Galeria do Azure, onde você pode selecionar por meio de muitos serviços
disponíveis do Azure. Na Galeria, selecione “Dados, armazenamento e
backup" na lista de categorias.

![Texto Alt][2]

A partir daqui, selecione a opção para Banco de Dados de Documentos do Azure

![Texto Alt][3]

Depois selecione “Criar" na parte inferior da tela

![Texto Alt][4]

Isso abrirá a blade “Novo Banco de Dados de Documentos”, onde você pode especificar o
nome, a região, a escala, o grupo de recursos e outras configurações para sua nova
conta.

![Texto Alt][5]

Depois de concluir a inserção de valores na conta, clique em “Criar”, e o processo de provisionamento começará a criar sua conta de banco de dados.
Quando o processo de provisionamento estiver concluído, você verá uma notificação
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

Agora vamos abordar como criar um novo aplicativo ASP.NET MVC
desde o início. Para sua referência, a solução completa pode ser
baixada [aqui].

# <a name="_Toc395637762">Criar um novo aplicativo ASP.NET MVC</a>

No Visual Studio, clique em Arquivo – Novo Projeto e selecione a opção para
criar um novo aplicativo Web ASP.NET MVC.

![Texto Alt][9]

Selecione onde você deseja criar o projeto e clique em Ok.

![Texto Alt][10]

Se você planeja hospedar seu aplicativo no Azure, marque a caixa na parte inferior direita para “Hospedar na nuvem". Selecionamos para hospedar na nuvem e executar o aplicativo hospedado no site do Azure. Selecionar
essa opção provisiona previamente um site do Azure para você e facilita a
vida quando o assunto é tempo para implantar o aplicativo de trabalho final.

Selecione OK e deixe o Visual Studio fazer isso realizando scaffolding do
modelo ASP.NET MVC vazio. É possível ver telas adicionais solicitando que você faça
logon na conta do Azure e forneça alguns valores para seu novo
site. Prossiga para fornecer todos esses valores do Azure e continue.

Depois que o Visual Studio concluir a criação do aplicativo MVC do texto clichê,
você terá um aplicativo ASP.NET vazio que poderá executar localmente.

Vamos ignorar a execução local porque tenho certeza de que vimos o aplicativo "Hello World" do ASP.NET. Vamos passar direto para a adição do Banco de Dados de Documentos a este projeto e a criação de nosso aplicativo.

# <a name="_Toc395637763">Configurando o aplicativo ASP.NET MVC</a>

### 

### <a name="_Toc395637764">Adicionar um modelo</a>

Vamos começar criando o **M** no MVC, o modelo. No Gerenciador de
Soluções, clique com o botão direito na pasta *Modelos* e clique em **Adicionar**. Em seguida, clique em
**Classe**

![Texto Alt][11]

Nomeie a nova Classe, **Item** e adicione o seguinte código a essa
nova Classe

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Todos os dados no Banco de Dados de Documentos são transferidos e armazenados como JSON. Para
controlar o modo como seus objetos são serializados/desserializados pelo JSON.NET, você
pode usar o atributo JsonProperty, conforme acima, que garante que os nomes
da propriedade sigam a convenção JSON de nomes de propriedade com letras minúsculas.
Você não **precisa** fazer isso.

### <a name="_Toc395637765">Adicionar um controlador</a>

Isso cuida do M. Agora, vamos criar o **C** no MVC, uma
classe de controlador.
 No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* e
clique em **Adicionar**. Em seguida, clique em **Controlador**.

![Texto Alt][12]

![Texto Alt][13]

Na caixa de diálogo **Adicionar Scaffold**, clique em **Controlador MVC 5 - Vazio.**
Clique em **Adicionar.**

![Texto Alt][14]

Nomeie o novo Controlador, **ItemController.**

Agora, o Visual Studio adicionará o ItemController. O Gerenciador de
Soluções deve ser semelhante ao seguinte.

![Texto Alt][15]

### <a name="_Toc395637766">Adicionar exibições</a>

E, por fim, vamos criar o **V** no MVC, uma exibição.

#### Adicionar exibição para índice de itens

Expanda a pasta ***Exibições*** no Gerenciador de Soluções e a localização da pasta
Item (vazia) que o Visual Studio criou quando
você adicionou o *ItemController* anteriormente. Clique com o botão direito do mouse no ***Item***
e escolha Adicionar uma nova Exibição.

![Texto Alt][16]

Na caixa de diálogo “Adicionar Exibição”. Chame a exibição "***Índice***", use o modelo
***Lista***, selecione o ***Item (Todo.Models)*** que
criamos anteriormente como classe e, por fim, use ***\_Layout.cshtml***
na Solução como página de Layout.

![Texto Alt][17]

Depois de definir todos esses valores, clique em Adicionar e deixe o Visual Studio criar
sua exibição para você. O Visual Studio criará uma exibição do modelo. Feito
isso, ele abrirá o arquivo cshtml criado. Podemos fechar este documento
no Visual Studio, pois voltaremos a ele mais tarde.

#### Adicionar exibição para novo item

De maneira semelhante ao que foi mostrado acima, crie uma nova Exibição para criar novos Itens
conforme o exemplo mostrado abaixo;

![Texto Alt][18]

#### Adicionar exibição para editar item

# <a name="_Toc395888515"></a>

E, por fim, adicione uma última Exibição para editar um Item da mesma maneira que foi feito
antes;

![Texto Alt][19]

Feito isso, feche os documentos cshtml no Visual Studio, pois voltaremos
a essas Exibições mais tarde.

</h1>
# <a name="_Toc395637767">Adicionando o Banco de Dados de Documentos ao seu projeto</a>

Isso cuida da maioria dos encanamentos ASP.NET MVC de que precisamos para
esta solução. Agora vamos abordar a finalidade real deste tutorial, adicionando o Banco de Dados de Documentos do Azure ao seu aplicativo Web.

</h1>
## <a name="_Toc395637768">Instalando o pacote NuGet do Banco de Dados de Documentos</a>

O SDK .NET do Banco de Dados de Documentos é empacotado e distribuído como um pacote NuGet.
Usando o gerenciador do pacote NuGet no Visual Studio (que você pode acessar
clicando com o botão direito no Projeto e escolhendo “Gerenciar Pacotes NuGet”,

![Texto Alt][20]

Procure online pelo "Banco de Dados de Documentos do Azure" e instale o pacote. Essa
ação baixará e instalará o pacote do Banco de Dados de Documentos, bem como todas as
dependências, como Newtonsoft.Json.

Depois de instalar o Visual Studio, a solução deverá se parecer com o seguinte,
com duas novas referências adicionadas;

![Texto Alt][21]

</h1>
## <a name="_Toc395637769">Conectando o Banco de Dados de Documentos</a>

### <a name="_Toc395637770">Listando itens incompletos</a>

Abra o **ItemController** e remova todo o código dentro da classe
(mas deixe a classe) que o Visual Studio adicionou. Vamos recompilá-lo parte por parte usando o Banco de Dados de Documentos.

Adicione o seguinte trecho de código à classe de ItemController
que agora está vazia;

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Ela usa uma classe de “pseudorrepositório" para o Banco de Dados de Documentos, que é, na verdade, apenas
uma classe Auxiliar que contém todo o código específico do Banco de Dados de Documentos. Para
os fins deste passo a passo, não vamos implementar uma camada completa de acesso
a dados com injeção de dependência usando um padrão de repositório,
como você poderia fazer se estivesse criando um aplicativo real. Para os
fins deste passo a passo, vamos apenas colocar toda a lógica de acesso
a dados em um projeto para manter a simplicidade.

Adicione uma nova Classe a seu projeto e chame-a de **DocumentDBRepository.**
E substitua o código no arquivo da classe pelo seguinte;

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Passe algum tempo adicionando o correto usando declarações para resolver as
referências. Todas as referências serão fáceis de resolver no Visual Studio desde
que o pacote NuGet tenha sido instalado com êxito. A referência para
*CreateDocumentQuery* será resolvida; depois você precisa adicionar manualmente o
seguinte usando a diretiva;

    using Microsoft.Azure.Documents.Linq; 

Há um pouco mais de encanamento manual que precisamos adicionar à nossa classe
pseudorrepositório para deixar tudo funcionando como deveria. Adicione o código
abaixo à sua classe de repositório;


        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

Quando você acessa pela primeira vez um método como GetIncompleteItems(), ele chama o
Cliente, que estabelecerá uma conexão com o Banco de Dados de Documento do Azure. A primeira
chamada para GetIncompleteItems() também tentará ler ou criar o
Banco de dados e a DocumentCollection para este aplicativo. Isso só precisa ser feito
uma vez. Depois, você pode armazenar em cache as propriedades SelfLink para
ambos os recursos e reutilizá-las nesse aplicativo.

Quando você deseja conectar-se ao Banco de Dados de Documentos posteriormente no mesmo aplicativo,
a mesma instância do Cliente, do Banco de dados e da Coleção será reutilizada,
em vez de criar uma nova instância toda vez.

Abra o **web.config** e adicione as seguintes linhas na
<appsettings\> seção;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
Neste ponto, sua solução deve ser capaz de compilar sem erros.

Se você executou o aplicativo agora, deve ir para o Controlador Inicial e
a exibição Índice desse controlador. Esse é o comportamento padrão para o projeto do modelo
MVC que escolhemos no início, mas não queremos isso! Vamos
mudar o roteamento neste aplicativo MVC para alterar seu comportamento.

Abra ***RouteConfig.cs*** na pasta App\_Start, localize a linha
começando com “padrões:" e altere-a para se parecer com o seguinte;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Agora, isso informa ao ASP.NET MVC que se você não tiver especificado um valor na URL para controlar o comportamento de roteamento, em vez de “Página Inicial”, ele deve usar "Item" como controlador e Índice do usuário como exibição.
Agora, se você executar o aplicativo, ele chamará seu **ItemController** e retornará os resultados do método **GetIncompleteItems** para a exibição Exibições\\Item\\Índice.
Se você executar este projeto agora, você deverá ver algo que se pareça com o seguinte;

![Texto Alt][22]

### <a name="_Toc395637771">Adicionando itens</a>

Agora vamos colocar alguns itens em nosso banco de dados; assim, temos algo mais
que uma grade vazia para observar.

Já temos uma Exibição para Criar e um Botão na Exibição de Índice
que levará o usuário para a exibição de criação. Vamos adicionar algum código ao
Controlador e ao Repositório para persistir no registro no Banco de Dados de Documentos.

Abra o ***ItemController.cs*** e adicione o seguinte trecho de código
que é como o ASP.NET MVC sabe o que fazer para a ação Criar, neste caso,
basta renderizar a exibição Create.cshtml associada criada anteriormente.

    public ActionResult Create()
    { 
          return View(); 
    }

Agora precisamos de mais algum código nesse controlador que aceitará o
envio a partir da exibição de criação.

Prossiga e adicione o próximo bloco de código que diz ao ASP.NET MVC o que
fazer com um formulário POST para esse controlador.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

O Controlador de Itens agora passará o Item, a partir do formulário, para o método
CreateDocument da nossa classe pseudorepositório, desse modo, adicione o
método a seguir à sua classe DocumentDBRepository.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Este método simplesmente pega algum objeto passado para ele e persiste-o no
Banco de Dados de Documentos.

Isso conclui o código exigido para adicionar novos itens ao nosso banco de dados.

### <a name="_Toc395637772">Editando itens</a>

Existe uma última ação para realizarmos, e esta é a capacidade de editar
itens no banco de dados e marcá-los como concluídos à medida que concluímos as
tarefas. Assim como na adição, a exibição para edição já foi adicionada, desse modo,
basta adicionar algum código adicional ao nosso Controlador e à classe
DocumentDBRepository novamente.

Adicione o seguinte à classe ItemController;

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

O primeiro método lida com o Http Get que ocorrerá quando o usuário
clicar no link “Editar" na exibição Índice. Esse método busca um
Documento do Banco de Dados de Documentos e o transfere para a exibição Editar.

### 

A exibição Editar realizará um Http Post de volta para o IndexController, e
o segundo método que adicionamos lida com essa transferência do objeto atualizado
através do Banco de Dados de Documentos a ser persistido no banco de dados.

Adicione o seguinte à classe DocumentDBRepository;

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

O primeiro desses dois métodos busca um Item do Banco de Dados de Documentos que é
transferido de volta para o ItemController e, em seguida, para a exibição Editar.

O segundo dos dois métodos que acabamos de adicionar substitui o documento no
Banco de Dados de Documentos pela versão do documento transferido do
ItemController.

Ou seja, isso é tudo que precisamos para executar nosso aplicativo, Listar
itens incompletos, Adicionar novos itens e, por fim, Editar itens.

</h3>
# <a name="_Toc395637773">Execute seu aplicativo localmente</a>

### 

Para testar o aplicativo no seu computador local, pressione F5 no Visual Studio
e ele deve compilar o aplicativo e iniciar um navegador com a página de grade
vazia que vimos anteriormente:

![Texto Alt][23]

1.Use os campos fornecidos para Item, Nome do Item e Categoria para inserir
informações, clique em **“Criar Novo"** e forneça alguns
valores. Deixe a caixa de seleção “Concluído" desmarcada, caso contrário, o novo item será
adicionado em um estado concluído e não aparecerá na lista inicial.

![Texto Alt][24]

Se clicar em Criar, você será redirecionado de volta para a página Índice e
espera-se que seu Item seja mostrado na Lista.

![Texto Alt][25]

Fique à vontade para adicionar mais alguns itens à sua lista de Tarefas Pendentes.

2.Clique em “Editar" perto de um Item na lista e você será levado
para a exibição Editar, onde poderá atualizar qualquer propriedade do objeto,
incluindo a marcação “Concluído”. Isso marca efetivamente o item como
concluído e o removerá da Lista de tarefas incompletas.

![Texto Alt][26]

3.Para concluir uma tarefa, basta marcar a caixa de seleção e clicar em
**Salvar.** Você será redirecionado de volta para a página de lista onde agora o
item não aparecerá mais na lista.

</h3>
# <a name="_Toc395637774">Implante o aplicativo nos sites do Azure</a>

### 

Agora que você possui o aplicativo completo funcionando corretamente no
Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure.

Se você selecionou “Hospedar na nuvem" quando criamos o projeto ASP.NET
MVC vazio, o Visual Studio tornará essa ação muito fácil e fará a maior parte
do trabalho para nós. Para Publicar este aplicativo, basta
clicar com o botão direito do mouse no Projeto no Gerenciador de Soluções (certifique-se de que você ainda
não o esteja executando localmente) e selecionar Publicar

![Texto Alt][27]

Tudo já deve estar configurado de acordo com suas credenciais;
na verdade, o site já foi criado no Azure para você na
"URL de Destino" mostrada. Basta clicar em **Publicar**

![Texto Alt][28]

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo
Web e iniciará um navegador onde você poderá ver seu trabalho
sendo executado no Azure!

</h3>
# <a name="_Toc395637775">Conclusão</a>

### 

Parabéns! Você acabou de criar seu primeiro Aplicativo ASP.NET MVC usando o Banco de Dados de Documentos do Azure e publicá-lo nos sites do Azure. O código-fonte para o aplicativo de referência completo pode ser baixado aqui.

[aqui] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Texto Alt]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
