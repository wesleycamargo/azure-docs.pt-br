<properties 
	pageTitle="Criando um aplicativo de Placas de Líderes com o back-end do .NET de Serviços Móveis do Azure" 
	description="Aprenda a criar um aplicativo da Windows Store usando serviços móveis do Azure com um back-end do .NET." 
	documentationCenter="windows" 
	authors="MikeWasson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mwasson"/>

# Criando um aplicativo de Placas de Líderes com o back-end do .NET de Serviços Móveis do Azure

Este tutorial mostra como compilar um aplicativo da Windows Store usando os Serviços Móveis do Azure com um back-end do .NET. Os Serviços Móveis do Azure fornecem um back-end escalonável e seguro com autenticação integrada, monitoração, notificações por push e outros recursos, mais uma biblioteca de cliente para compilar aplicativos móveis em várias plataformas. O back-end do .NET dos Serviços Móveis é baseado em [ASP.NET Web API](http://asp.net/web-api), e oferece aos desenvolvedores .NET uma maneira superior de criar APIs REST.   

+ [Visão geral] 
+ [Sobre o aplicativo de exemplo] 
+ [Adicionar modelos de dados]
+ [Adicionar controladores de API Web]
+ [Usar um DTO para retornar entidades relacionadas]
+ [Definir uma API personalizada para enviar pontuações]
+ [Criar o aplicativo da Windows Store]
+ [Adicionar classes de modelo]
+ [Criar um modelo de exibição]
+ [Adicionar uma instânciaMobileServiceClient]
+ [Criar a página principal]
+ [Publicar o serviço móvel]
+ [Próximas etapas]

## Visão geral

API Web é uma estrutura de software livre que oferece aos desenvolvedores de .NET uma maneira superior de criar APIs REST. Você pode hospedar uma solução de API Web em sites do Azure, em Serviços Móveis do Azure, usando o back-end do .NET, ou mesmo de auto-hospedagem em um processo personalizado. Os Serviços Móveis estão em um ambiente de hospedagem que é projetado especialmente para aplicativos móveis. Quando você hospeda seu serviço API Web em Serviços Móveis, obtém as seguintes vantagens, além do armazenamento de dados:

- Autenticação integrada com provedores sociais e Azure Active Directory (AAD). 
- Notificações por push para aplicativos que usam serviços de notificação específicos de dispositivos.
- Um conjunto completo de bibliotecas clientes que facilita o acesso ao seu serviço de qualquer aplicativo. 
- Logon integrado e diagnósticos.

Neste tutorial, você vai::

- Criar uma API REST usando os Serviços Móveis do Azure.
- Publicar o serviço no Azure.
- Criar um aplicativo da Windows Store que utiliza o serviço.
- Usar Entity Framework (EF) para criar relações de chave estrangeira e objetos e transferência de dados (DTOs).
- Usar API Web de ASP.NET para definir uma API personalizada.

Este tutorial usa o [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/p/?LinkID=390465). 


## Sobre o aplicativo de amostra

Um*placar de líderes* mostra uma lista de jogadores em um jogo, juntamente com suas pontuações e a classificação de cada jogador. Um placar de líderes pode ser parte de um grande jogo, ou pode ser um aplicativo separado. Um placar de líderes é um aplicativo verdadeiro, mas é simples o bastante para um tutorial. Aqui está uma captura de tela do aplicativo:

![][1]

Para manter o aplicativo simples, não há jogo real. Em vez disso, você pode adicionar jogadores e enviar uma pontuação para cada jogador. Quando você envia uma pontuação, o serviço móvel calcula as novas classificações. No back-end, o serviço móvel cria um banco de dados com duas tabelas:

- Player. Contém a ID do jogador e o nome.
- PlayerRank. Contém a pontuação e a classificação do jogador.

PlayerRank possui uma chave estrangeira para Player. Cada jogador possui zero ou um PlayerRank.

Em um aplicativo de placar de líder real, o PlayerRank também pode ser uma ID do jogo, assim um jogador pode enviar pontuações de mais de um jogo.

![][2]

O aplicativo cliente pode executar um conjunto completo de operações CRUD nos Jogadores. Ele pode ler ou excluir entidades existentes do PlayerRank, mas não pode criá-las ou atualizá-las diretamente. Isso porque o valor de classificação é calculado pelo serviço. Em vez disso, o cliente envia uma pontuação e o serviço atualiza as classificações de todos os jogadores.

Baixe o projeto completo [aqui](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).


## Criar o projeto

Ative o Visual Studio e crie um novo projeto de Aplicativo Web ASP.NET. Nomeie o Placar de líderes do projeto.

![][3]

No Visual Studio 2013 Atualização 3,o projeto de Aplicativo Web ASP.NET inclui um modelo para o Serviço Móvel do Microsoft Azure. Selecione esse modelo e clique em **OK**.

![][4]
 
O modelo do projeto inclui um controlador de exemplo e objeto de dados.  

![][5]
 
Eles não são necessários para o tutorial, portanto, você pode excluí-los do projeto. Também remova as referências ao TodoItem em WebApiConfig.cs e LeaderboardContext.cs.

## Adicionar modelos de dados

Você usará o [Primeiro código EF](http://msdn.microsoft.com/data/ee712907#codefirst) para definir as tabelas de banco de dados. Na pasta DataObjects, adicione uma classe chamada `Player`.

	using Microsoft.WindowsAzure.Mobile.Service;
	
	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

Adicione outra classe chamada `PlayerRank`.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;
	
	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }
	
	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

Observe que as duas classes são herdeiras da classe **EntityData**. Extraindo de **EntityData** fica fácil para o aplicativo consumir os dados, usando a biblioteca cliente de várias plataformas para os Serviços Móveis do Azure. **EntityData** também facilita para que um aplicativo [manipule conflitos de gravação do banco de dados](http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/).

A classe `PlayerRank` possui uma [propriedade de navegação](http://msdn.microsoft.com/data/jj713564.aspx) que aponta para a entidade `Player` relacionada. O atributo **[ForeignKey]** diz à EF que a propriedade `Player` representa uma chave estrangeira.

# Adicionar controladores de API Web

Em seguida, você adiciona controladores de API Web a `Player` e `PlayerRank`. Em vez de controladores de API Web simples, você adicionará um tipo especial de controlador chamado *controlador de tabela*, projetado especialmente para os Serviços Móveis do Azure.

Clique com o botão direito do mouse na pasta Controladores, selecione Adicionar e selecione Novo item Scaffolded.

![][6] 

Na caixa de diálogo **Adicionar Scaffold** expanda **Comum** à esquerda e selecione **Serviços Móveis do Microsoft Azure**. Em seguida, selecione **Controlador de Tabela dos Serviços Móveis do Microsoft Azure**. Clique em **Adicionar**.

![][7] 
 
No diálogo **Adicionar controlador**:

1.	Em **Classe de modelo**, selecione Player. 
2.	Em **Classe de contexto de dados**, selecione MobileServiceContext.
3.	Nomeie o controlador "ControladorDeRankDoJogador".
4.	Clique em **Adicionar**.


Esta etapa adiciona um arquivo chamado PlayerController.cs ao projeto.

![][8] 

O controlador obtém de **TableController<T>**. Esta classe herda **ApiController**, mas é especializado para Serviços Móveis do Azure.
 
- Roteamento: A rota padrão para um **TableController** é `/tables/{table_name}/{id}`, em que *table_name* corresponde ao nome da entidade. A rota para o controlador do Player é */tables/player/{id}*. Essa convenção de roteamento deixa o **TableController** consistente com os Serviços Móveis do [API REST](http://msdn.microsoft.com/library/azure/jj710104.aspx).
- Acesso de dados: Para operações de banco de dados, a classe **TableController** usa a interface **IDomainManager**, que define uma abstração para acesso a dados.  O scaffolding usa **EntityDomainManager**, que é uma implementação concreta de **IDomainManager** que encapsula um contexto EF. 

Agora, adicione um segundo controlador às entidades do PlayerRank. Siga as mesmas etapas, mas escolha o PlayerRank para a classe do modelo. Use a mesma classe de contexto de dados; não crie uma nova. Nomeie o controlador "PlayerRankController".

## Usar um DTO para retornar entidades relacionadas

Lembre-se que `PlayerRank` tem uma `Player` entidade:relacionada 

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

A biblioteca do cliente do Serviço Móvel não dá suporte a propriedades de navegação, e elas não serão serializadas. Por exemplo, aqui está a resposta de HTTP bruta para GET `/tables/PlayerRank`:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT
	
	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Observe que o `Player` não está incluído no gráfico de objeto. Para incluir o player, podemos nivelar o gráfico do objeto, definindo um *DTO* (Objeto de Transferência de Dados). 

Um DTO é um objeto que define o quanto de dados será enviado pela rede. Os DTOs são úteis sempre que você deseja vincular o formato a uma aparência diferente do modelo de seu banco de dados. Para criar um DTO para `PlayerRank`, adicione uma nova classe chamada `PlayerRankDto` à pasta DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

Na classe `PlayerRankController` usaremos o método LINQ**Select** para converter instâncias de  `PlayerRank` em instâncias de `PlayerRankDto`. Atualize os métodos do controlador `GetAllPlayerRank` e`GetPlayerRank` como segue:

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}
	
	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	
	    return SingleResult<PlayerRankDto>.Create(result);
	}

Com essas alterações, os dois métodos GET retornam objetos `PlayerRankDto` ao cliente. A propriedade `PlayerRankDto.PlayerName` é definida como o nome do player. Aqui está uma resposta de exemplo depois de aplicar essas alterações:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT
	
	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Observe que a carga JSON agora inclui os nomes dos jogadores.

Em vez de usar as instruções de Seleção LINQ, outra opção é usar o AutoMapper. Essa opção requer algum código de configuração adicional, mas habilita o mapeamento automático de entidades de domínio para DTOs. Para obter mais informações, consulte [Mapeamento entre tipos de banco de dados e tipos de clientes no back-end do .NET usando o AutoMapper](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx).

## Definir uma API personalizada para enviar pontuações

A entidade `PlayerRank` inclui uma propriedade `Rank`. Esse valor é calculado pelo servidor e não desejamos que os clientes configurem isso. Em vez disso, os clientes usarão uma API personalizada para enviar uma pontuação do jogador.  Quando o servidor estiver com uma nova pontuação, ele atualizará toda a classificação dos jogadores.

Primeiro, adicione uma classe chamada `PlayerScore` à pasta DataObjects.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Na classe `PlayerRankController`, mova a variável `MobileServiceContext` do construtor para uma variável de classe:

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


Exclua os seguintes métodos de `PlayerRankController`:

- `PatchPlayerRank` 
- `PostPlayerRank` 
- `DeletePlayerRank`

Em seguida, adicione o seguinte código a `PlayerRankController`:

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

O método `PostPlayerScore` usa uma instância `PlayerScore` como entrada. (O cliente enviará o `PlayerScore` em uma solicitação HTTP POST.) O método faz o seguinte:

1.	Adiciona um novo`PlayerRank` ao player, se ainda não houver um no banco de dados.
2.	Atualiza a pontuação do jogador.
3.	Executa uma consulta SQL que atualiza em lote todas as classificações de jogador.

O atributo **[Route]** para definir uma rota personalizada para este método:

	[Route("api/score")]

Você também pode colocar o método em um controlador separado. Não há nenhuma vantagem em particular de qualquer modo, ele depende apenas de como você deseja organizar seu código.
Para saber mais sobre o atributo **[Route]** consulte [Roteamento de atributo na API Web](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2).

## Criar o aplicativo da Windows Store

Nesta seção, descrevemos o aplicativo da Windows Store que consome o serviço móvel. Entretanto, o objetivo não é focalizar muito no XAML ou na interface do usuário. Em vez disso, desejamos nos concentrar na lógica do aplicativo. Você pode baixar o projeto completo [aqui](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af).

Adicionar um novo projeto de aplicativo da Windows Store à solução. Eu usei o modelo de aplicativo em branco (Windows). 

![][10]
 
Usar o Gerenciador de pacotes NuGet para adicionar a biblioteca cliente dos Serviços Móveis. No Visual Studio, no menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet**. Em seguida, selecione o **Console do Gerenciador de Pacotes**. Na janela do Console do Gerenciador de Pacotes, digite o seguinte comando.

	Pacote de instalação Mobileservices - LeaderboardApp do projeto

A chave -Project especifica em qual projeto instalar o pacote.

## Adicionar classes de modelo

Criar uma pasta chamada Modelos e adicionar as seguintes classes:

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }
	
	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Essas classes correspondem diretamente às entidades de dados no serviço móvel.
 
## Criar um modelo de exibição

Model-View-ViewModel (MVVM) é uma variante de Model-View-Controller (MVC). O padrão MVVM ajuda a separar a lógica do aplicativo da apresentação.

- O modelo representa os dados do domínio (jogador, classificação do jogador e pontuação do jogador).
- O modelo de exibição é uma representação abstrata da exibição. 
- A exibição mostra o modelo de exibição e envia as entradas do usuário para o modelo de exibição. Para um aplicativo da Windows Store, a exibição é definida em XAML.

![][11] 

Adicione uma classe denominada `LeaderboardViewModel`.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;
	
	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;
	
	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

Implemente **INotifyPropertyChanged** no modelo de exibição, de modo que o modelo de exibição possa participar da vinculação de dados. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }    
    }

Em seguida, adicione propriedades observáveis. O XAML associará dados a essas propriedades. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

A propriedade `IsPending` será verdadeira enquanto uma operação assíncrona estiver pendente no serviço. A propriedade `ErrorMessage` contém qualquer mensagem de erro do serviço. 

Finalmente, adicione métodos que chamam por meio de camada de serviço. 

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            }; 
            
            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }    
    }

## Adicionar uma instância MobileServiceClient

Abra o arquivo App.xaml.cs e adicione uma instância do **MobileServiceClient** à classe `App`.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;
	
	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);
	
	
	        // ...
	    }
	}

Quando você depurar localmente, o serviço móvel será executado em IIS express. O Visual Studio atribui um número de porta aleatório, portanto, a URL local é http://localhost:*port*, em que *port* é o número de porta. Para obter o número de porta, inicie o serviço no Visual Studio pressionando F5 para depurar. O Visual Studio ativará um navegador e navegará para a URL de serviço.  Você também pode localizar a URL local nas propriedades do projeto, em **Web**.

## Criar a página principal

Na página principal, adicione uma instância do modelo de exibição. Em seguida, defina o modelo de exibição como **DataContext** para a página.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


Como mencionado anteriormente, não mostraremos todo o XAML para o aplicativo. Um benefício do padrão MVVM é separar a apresentação da lógica do aplicativo, isso facilita a alteração da interface do usuário, caso você não goste do aplicativo de amostra.

É exibida a lista de players em uma **ListBox**:

	<ListBox Width="200" Height="400" x:Name="PlayerListBox" 
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

As classificações são exibidas em uma **ListView**:

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

Toda a vinculação de dados ocorre por meio do modelo de exibição.

## Publicar o serviço móvel

Nesta etapa, você publicará seu serviço móvel no Microsoft Azure e modificará o aplicativo para usar o serviço ativo.

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto Placar de Líderes e selecione **Publicar**.
 
![][12]

No diálogo **Publicar**, clique em **Serviços Móveis do Microsoft Azure**.

![][13]
 
Se você ainda não tiver entrado em sua conta do Azure, clique em **Entrar**.

![][14]


Selecione um Serviço Móvel existente ou clique em **Novo** para criar um novo. Em seguida, clique em **OK** para publicar.

![][15]
 
O processo de publicação cria automaticamente o banco de dados. Não é necessário configurar uma cadeia de conexão.

Agora você está pronto para conectar o aplicativo Placar de Líderes ao serviço ativo. Duas coisas são necessárias:

- A URL do serviço
- A chave do aplicativo

Você pode obter ambos no Portal de Gerenciamento do Azure. No Portal de Gerenciamento, clique em **Serviços Móveis**, em seguida, clique no serviço móvel. A URL de serviço é listada na guia do painel. Para obter a chave do aplicativo, clique em **Gerenciar Chaves**.

![][16]
 
No diálogo **Gerenciar Chaves de Acesso**, copie o valor da chave do aplicativo.

![][17]

 
Passe a URL do Serviço e a chave do aplicativo ao construtor **MobileServiceClient**.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

Agora, quando executar o aplicativo, ele se comunicará com o serviço real. 

## Próximas etapas

* [Saiba mais sobre os Serviços Móveis do Azure]
* [Saiba mais sobre API Web]
* [Tratar conflitos de gravação do banco de dados]
* [Adicionar notificações por push]; por exemplo, quando alguém adicionar um novo jogador ou atualizar uma pontuação.
* [Introdução à autenticação]

<!-- Anchors. -->
[Visão geral]: #overview
[Sobre o aplicativo de amostra]: #about-the-sample-app
[Criar o projeto]: #create-the-project
[Adicionar modelos de dados]: #add-data-models
[Adicionar controladores de API Web]: #add-web-api-controllers
[Usar um DTO para retornar entidades relacionadas]: #use-a-dto-to-return-related-entities
[Definir uma API personalizada para enviar pontuações]: #define-a-custom-api-to-submit-scores
[Criar o aplicativo da Windows Store]: #create-the-windows-store-app
[Adicionar classes de modelo]: #add-model-classes
[Criar um modelo de exibição]: #create-a-view-model
[Adicionar uma instânciaMobileServiceClient]: #add-a-mobileserviceclient-instance
[Criar a página principal]: #create-the-main-page
[Publicar o serviço móvel]: #publish-your-mobile-service
[Próximas etapas]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Saiba mais sobre os Serviços Móveis do Azure]: /pt-br/develop/mobile/resources/
[Saiba mais sobre API Web]: http://asp.net/web-api
[Tratar conflitos de gravação do banco de dados]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
[Adicionar notificações por push]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet

\<!--HONumber=42-->
