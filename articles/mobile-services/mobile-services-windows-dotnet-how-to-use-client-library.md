<properties
	pageTitle="Trabalhando com a biblioteca de cliente do .NET para Serviços Móveis"
	description="Aprenda a usar um cliente .NET para serviços móveis do Azure."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="glenga"/>

# Como usar um cliente .NET para os Serviços Móveis do Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Visão geral

Este guia mostra como executar cenários comuns usando um cliente .NET para os Serviços Móveis do Azure, em aplicativos da Windows Store e em aplicativos do Windows Phone. Os cenários abrangidos incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se for novo nos Serviços Móveis, primeiro conclua o tutorial [Início rápido dos Serviços Móveis](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuração e Pré-requisitos

Vamos pressupor que você criou um serviço móvel e uma tabela. Para obter mais informações, consulte [Criar uma tabela](http://go.microsoft.com/fwlink/?LinkId=298592). No código usado neste tópico, a tabela é denominada `TodoItem` e terá as seguintes colunas: `Id`, `Text` e `Complete`.

O tipo .NET do lado do cliente tipado correspondente é o seguinte:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Quando o esquema dinâmico está habilitado, os Serviços Móveis do Azure geram automaticamente novas colunas com base no objeto de solicitações de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://go.microsoft.com/fwlink/?LinkId=296271).

##<a name="create-client"></a>Como criar o cliente dos Serviços Móveis

O código seguinte cria o objeto `MobileServiceClient` que é usado para acessar seu serviço móvel.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

No código acima, substitua `AppUrl` e `AppKey` pela URL e pela chave do aplicativo do serviço móvel, nessa ordem. Ambas estão disponíveis no Portal de Gerenciamento do Azure, selecionando seu serviço móvel e clicando em "Painel".

>[AZURE.IMPORTANT]A chave do aplicativo serve para filtrar solicitações aleatórias de seu serviço móvel e é distribuída com o aplicativo. Como essa chave não é criptografada, ela não pode ser considerado segura. Para realmente proteger o acesso aos seus dados de serviços móveis, você deve autenticar os usuários antes de permitir o acesso. Para obter mais informações, consulte [Como autenticar usuários](#authentication).

##<a name="instantiating"></a>Como criar uma referência de tabela

Todo o código que acessa ou modifica dados na tabela dos Serviços Móveis chama funções no objeto `MobileServiceTable`. Você obtém uma referência à tabela chamando a função [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) em uma instância do `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Esse é o modelo de serialização tipado. Consulte a discussão sobre o <a href="#untyped">modelo de serialização não tipado</a> abaixo.

##<a name="querying"></a>Como consultar dados de um serviço móvel

Esta seção descreve como emitir consultas para o serviço móvel, que inclui as seguintes funcionalidades:

- [Filtrar dados retornados]
- [Classificar dados retornados]
- [Retornar dados em páginas]
- [Selecionar colunas específicas]
- [Pesquisar dados por ID]

>[AZURE.NOTE]Um tamanho de página controlado por servidor é usado para impedir que todas as linhas sejam retornadas. Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço. Para retornar mais de 50 linhas, use o método `Take`, conforme descrito em [Retornar dados em páginas].

### <a name="filtering"></a>Como filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula `Where` em uma consulta. Ele retorna todos os itens de `todoTable`, cuja propriedade `Complete` é igual a `false`. A função `Where` aplica um predicado de filtragem de linha à consulta na tabela.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Você pode exibir o URI da solicitação enviado ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. Se você examinar o URI abaixo, observe que estamos modificando a própria cadeia de caracteres da consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Essa solicitação normalmente é traduzida aproximadamente para a seguinte consulta SQL no lado do servidor:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

A função passada para o método `Where` pode ter um número arbitrário de condições. Por exemplo, a linha abaixo:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

É traduzida aproximadamente (para a mesma solicitação mostrada anteriormente) como

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

A instrução `where` acima localizará itens com o status `Complete` definido como falso com `Text` não nulo.

Em vez disso, também poderíamos ter escrito isso em várias linhas:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável. A opção anterior -- de concatenar vários predicados em uma consulta – é mais compacta e recomendada.

A cláusula `where` oferece suporte a operações que são traduzidas para o subconjunto OData dos Serviços Móveis. Isso inclui os operadores relacionais (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisão de número (Math.Floor, Math.Ceiling), funções de cadeias de caracteres (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propriedades de data (Year, Month, Day, Hour, Minute, Second), propriedades de acesso de um objeto e expressões que combinam todos eles.

### <a name="sorting"></a>Como classificar dados retornados

O código a seguir ilustra como classificar dados incluindo uma função `OrderBy` ou `OrderByDescending` na consulta. Ele retorna os itens da `todoTable` classificada em ordem crescente pelo campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Como retornar dados em páginas

Por padrão, o servidor retorna apenas as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [Take]. Use `Take` juntamente com o método [Ignorar] para solicitar uma "página" específica do conjunto de dados total retornado pela consulta. A consulta a seguir, quando executada, retorna os três itens principais na tabela.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

A seguinte consulta revisada ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Você também pode usar o método [IncludeTotalCount] para garantir que a consulta obterá a contagem total de <i>todos</i> os registros que seriam retornados, ignorando qualquer cláusula de tomada de paginação/limite especificada:

	query = query.IncludeTotalCount();

Esse é um cenário simplificado de passagem de valores de paginação embutidos em código para os métodos `Take` e `Skip`. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores.

####Considerações de paginação para um serviço móvel de back-end em .NET

Para substituir o limite de 50 linhas em um serviço móvel de back-end do .NET, você também deve aplicar o [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) ao público método GET e especificar o comportamento de paginação. Quando aplicado ao método, o seguinte define o máximo de linhas retornadas para 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Como selecionar colunas específicas

Você pode especificar o conjunto de propriedades para incluir nos resultados, adicionando uma cláusula `Select` à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Todas as funções descritas até agora são aditivas, portanto, podemos continuar a chamá-las e a afetar a consulta a cada vez. Mais um exemplo:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Como pesquisar dados pela ID

A função `LookupAsync` pode ser usada para procurar objetos do banco de dados com uma determinada ID.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

##<a name="inserting"></a>Como inserir dados em um serviço móvel

> [AZURE.NOTE]Se você desejar executar operações de inserção, pesquisa, exclusão ou atualização em um tipo, será necessário criar um membro chamado **Id**. É por isso que a classe de exemplo **TodoItem** tem um membro de nome **Id**. Um valor de id válido deve sempre estar presente em operações de atualização e exclusão.

O código a seguir ilustra como inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

	await todoTable.InsertAsync(todoItem);

Se um valor exclusivo de ID personalizada não estiver incluído no `todoItem` passado para a chamada de `todoTable.InsertAsync`, será gerado um valor de ID pelo servidor, que será definido no objeto `todoItem` retornado para o cliente.

Para inserir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado abaixo.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Aqui está um exemplo usando um endereço de email como uma id de cadeia de caracteres exclusiva.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###Trabalhando com valores de ID

Os Serviços Móveis oferecem suporte a valores exclusivos e personalizados de cadeia de caracteres para a coluna **id** da tabela. Isso permite que aplicativos usem valores personalizados, como endereços de email ou nomes de usuário para a ID.

IDs de cadeia de caracteres fornecem os seguintes benefícios:

+ As IDs são geradas sem fazer uma viagem ida e volta ao banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de IDs podem integrar-se melhor a uma lógica do aplicativo.

Quando um valor de ID de cadeia de caracteres não está definido em um registro inserido, os Serviços Móveis geram um valor exclusivo para a ID. Você pode usar o método `Guid.NewGuid()` para gerar seus próprios valores de ID, no cliente ou em um serviço móvel de back-end do .NET. Para saber mais sobre como gerar GUIDs em um serviço móvel de back-end JavaScript, consulte [Como gerar valores exclusivos de ID](mobile-services-how-to-use-server-scripts.md#generate-guids).

Você também pode usar IDs de números inteiros para suas tabelas. Para usar uma ID de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId`. Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas de Serviços Móveis](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Como modificar dados em um serviço móvel

O código a seguir ilustra como atualizar uma instância existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

	await todoTable.UpdateAsync(todoItem);


Para inserir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma atualização, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser atualizada. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Se você tentar atualizar um item sem fornecer o valor da "Id", o serviço não poderá identificar a instância a ser atualizada e, portanto, o SDK dos Serviços Móveis gerará uma `ArgumentException`.


##<a name="deleting"></a>Como excluir dados em um serviço móvel

O código a seguir ilustra como excluir dados de uma instância existente. A instância é identificada pelo campo "Id" definido em `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para excluir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma solicitação de exclusão, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser excluída. Uma solicitação de exclusão precisa apenas da ID. Outras propriedades não são passadas para o serviço e, se for passada alguma propriedade, ela será ignorada no serviço. O resultado de uma chamada de `DeleteAsync`, geralmente, também é `null`. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Se você tentar excluir um item sem o campo "Id" já estar definido, o serviço não poderá identificar qual instância excluir e, portanto, você obterá uma `MobileServiceInvalidOperationException` do serviço. Da mesma forma, se tentar excluir um item não tipado sem o campo "Id" já definido, você também obterá uma `MobileServiceInvalidOperationException` do serviço.

##<a name="#custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON. Para obter um exemplo de como criar uma API personalizada em seu serviço móvel, consulte [Como definir um ponto de extremidade de API personalizado](mobile-services-dotnet-backend-define-custom-api.md).

Você pode chamar uma API personalizada chamando uma das sobrecargas de método [InvokeApiSync] no cliente. Por exemplo, a linha de código a seguir envia uma solicitação POST à API **completeAll** no serviço móvel local:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Observe que esta é uma chamada de método tipada, que exige que o tipo de retorno **MarkAllResult** seja definido. Os dois métodos, tipado e não tipado, são aceitos. Este é um exemplo quase trivial, pois é tipado, não envia carga, não possui parâmetros de consulta e não altera os cabeçalhos de solicitação. Para exemplos mais realistas e uma discussão mais abrangente do [InvokeApiAsync], consulte [API personalizada nas SDKs do cliente dos Serviços Móveis do Azure].

##Como registrar para notificações de push

O cliente de Serviços Móveis permite que você se registrar para notificações por push com Hubs de Notificação do Azure. Durante o registro, você pode obter um URI de canal que você obtém da plataforma Windows. Então fornece este valor, juntamente com quaisquer marcas, no momento em que cria o registro. O código a seguir registra as notificações por push em um aplicativo da Windows Store que use o serviço de notificação do Windows (WNS):

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

Observe que, neste exemplo, são incluídas duas marcas com o registro. Para obter mais informações, consulte [Adicionar notificações por push para seu aplicativo](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)

>[AZURE.NOTE]Quando você precisar enviar notificações a determinados usuários registrados, é importante exigir autenticação antes do registro e, em seguida, verificar se o usuário está autorizado para registrar com uma marca específica. Por exemplo, você deve verificar para certificar-se de que um usuário não se registra com uma marca de que seja o ID de outro usuário. Para obter mais informações, consulte [Enviar notificações push para usuários autenticados](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).


##<a name="optimisticconcurrency"></a>Como usar simultaneidade otimista

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O Controle de Simultaneidade Otimista pressupõe que cada transação pode ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida.

Os Serviços Móveis oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema `__version` que é definida para cada tabela criada pelos Serviços Móveis. Cada vez que um registro é atualizado, os Serviços Móveis definem a propriedade `__version` desse registro como um novo valor. Durante cada solicitação de atualização, a propriedade `__version` do registro incluído na solicitação é comparada à mesma propriedade do registro no servidor. Se a versão passada com a solicitação não corresponder à do servidor, a biblioteca de cliente .NET dos Serviços Móveis gerará uma `MobileServicePreconditionFailedException<T>`. O tipo incluído com a exceção é o registro do servidor que contém a versão do registro do servidor. O aplicativo pode usar essas informações para decidir se deve executar a solicitação de atualização novamente com o valor de `__version` correto do servidor para confirmar as alterações.

Para habilitar a simultaneidade otimista, o aplicativo define uma coluna na classe da tabela para a propriedade do sistema `__version`. A definição a seguir fornece um exemplo.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Aplicativos que usam tabelas não tipadas habilitam a simultaneidade otimista definindo o sinalizador `Version` nas `SystemProperties` da tabela da seguinte maneira.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


O código a seguir mostra como resolver um conflito de gravação quando detectado. O valor correto de `__version` deve ser incluído na chamada de `UpdateAsync()` para confirmar uma resolução.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}


Para obter um exemplo mais completo de como usar a simultaneidade otimista nos Serviços Móveis, consulte o [Tutorial de simultaneidade otimista].


##<a name="binding"></a>Como vincular dados à interface do usuário em um serviço móvel

Esta seção mostra como exibir os objetos de dados retornados usando elementos da interface do usuário. Para consultar itens incompletos em `todoTable` e exibi-los em uma lista muito simples, você pode executar o seguinte código de exemplo para vincular a origem da lista a uma consulta. O uso de `MobileServiceCollection` cria uma coleção de associações com reconhecimento de serviços móveis.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Alguns controles no Tempo de Execução do Windows oferecem suporte a uma interface chamada [ISupportIncrementalLoading (a página pode estar em inglês)](http://msdn.microsoft.com/library/windows/apps/Hh701916). Essa interface permite que os controles solicitem dados adicionais quando o usuário rola. Há suporte interno para essa interface para aplicativos da Windows Store via `MobileServiceIncrementalLoadingCollection`, que manipula automaticamente as chamadas dos controles. Para usar `MobileServiceIncrementalLoadingCollection` em aplicativos da Windows Store, proceda da seguinte maneira:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Para usar a nova coleção no Windows Phone, use os métodos da extensão `ToCollection` em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para efetivamente carregar dados, chame `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Quando usa a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você obtém uma coleção que pode ser vinculada a controles da interface do usuário. Essa coleção tem reconhecimento de paginação, ou seja, um controle pode pedir que a coleção "carregue mais itens", e a coleção fará isso para o controle. Nesse momento não há nenhum código de usuário envolvido, o controle iniciará o fluxo. No entanto, como a coleção está carregando dados da rede, é esperado que haja falhas nesse carregamento algumas vezes. Para lidar com essas falhas, é possível substituir o método `OnException` na `MobileServiceIncrementalLoadingCollection` para tratar das exceções resultantes de chamadas para `LoadMoreItemsAsync` executadas pelos controles.

Finalmente, imagine que sua tabela tenha muitos campos, mas você só deseja exibir alguns deles em seu controle. Você pode usar as diretrizes da seção <a href="#selecting">"Selecionar colunas específicas”</a> acima para selecionar colunas específicas a serem exibidas na interface do usuário.

##<a name="authentication"></a>Como autenticar usuários

Os Serviços Móveis oferecem suporte à autenticação e à autorização de usuários de aplicativo, usando vários provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e o Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo].

Dois fluxos de autenticação têm suporte: um _fluxo de servidor_ e um _fluxo de cliente_. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos ao dispositivo pois depende dos SDKs específicos ao provedor e ao dispositivo.

###Fluxo de servidor
Para que os Serviços Móveis gerenciem o processo de autorização em seu aplicativo da Windows Store ou do Windows Phone, você deve registrar seu aplicativo com o provedor de identidade. Em seguida, no seu serviço móvel, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo].

Depois de registrar o provedor de identidade, basta chamar o [método LoginAsync] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, o código a seguir inicia uma entrada de fluxo do servidor usando o Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Se você estiver usando um provedor de identidade além do Facebook, altere o valor [MobileServiceAuthenticationProvider] acima para o valor de seu provedor.

Neste caso, os Serviços Móveis gerenciam o fluxo de autenticação OAuth 2.0 exibindo a página de entrada do provedor selecionado e gerando um token de autenticação dos Serviços Móveis após um logon bem-sucedido no provedor de identidade. O [método LoginAsync] retorna um [MobileServiceUser], que fornece a [userId] do usuário autenticado e o [MobileServiceAuthenticationToken] como um JWT (token da web JSON). Esse token pode ser armazenado em cache e reutilizado até que expire. Para obter mais informações, consulte [Armazenando o token de autenticação em cache].

###Fluxo de cliente

Seu aplicativo também pode entrar em contato independentemente com o provedor de identidade e fornecer o token retornado aos Serviços Móveis para autenticação. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade.

####Entrada única usando um token do Facebook ou do Google

No formulário mais simplificado, você pode usar o fluxo de cliente conforme mostrado neste trecho de código para o Facebook ou o Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}


####Entrada única usando a Conta da Microsoft com o Live SDK

Para poder autenticar usuários, você deverá registrar seu aplicativo na Central de desenvolvedores da conta da Microsoft. Em seguida, você deve conectar esse registro ao serviço móvel. Conclua as etapas do tópico [Registrar seu aplicativo para usar um logon de conta da Microsoft](mobile-services-how-to-register-microsoft-authentication.md) a fim de criar um registro de conta da Microsoft e conectá-lo ao seu serviço móvel. Se você tiver as versões para a Windows Store e para o Windows Phone de seu aplicativo, registre a versão da Windows Store primeiro.

O código a seguir é autenticado usando o Live SDK e usa o token retornado para entrar em seu serviço móvel.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the mobile service.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the mobile service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to Mobile Services.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }


###<a name="caching"></a>Armazenando em cache o token de autenticação
Em alguns casos, a chamada para o método login pode ser evitada após a primeira vez que o usuário é autenticado. Você pode usar o [PasswordVault] para aplicativos da Windows Store para armazenar a identidade do usuário atual em cache na primeira vez que fizer logon e em todas as vezes subsequentes em que você verificar se já tem a identidade do usuário em nosso cache. Quando o cache estiver vazio, ainda será necessário enviar o usuário pelo processo de logon.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


Para aplicativos do Windows Phone, você pode criptografar e armazenar os dados em cache usando a classe [ProtectedData] e armazenar informações confidenciais em um armazenamento isolado.

##<a name="errors"></a>Como tratar erros

Há várias maneiras de encontrar, validar e resolver erros nos Serviços Móveis.

Por exemplo, os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Imagine definir e registrar um script de servidor que valide e modifique dados, da seguinte forma:

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Esse script do lado do servidor valida o comprimento dos dados da cadeia de caracteres enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

Agora que o serviço móvel está validando dados e enviando respostas de erros no lado do servidor, você pode atualizar seu aplicativo .NET para que possa tratar respostas de erros na validação.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

##<a name="untyped"></a>Como trabalhar com dados não tipados

O cliente .NET foi desenvolvido para cenários fortemente tipados. No entanto, às vezes, uma experiência menos rígida é conveniente, por exemplo, ao lidar com objetos com esquema aberto. Esse cenário é habilitado da seguinte forma. Em consultas, você abandona o LINQ e usa o formato de conexão.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Você recupera valores JSON que podem ser usados como um recipiente de propriedades. Para obter mais informações sobre JToken e Json.NET, consulte [Json.NET](http://json.codeplex.com/)

##<a name="unit-testing"></a>Como criar testes de unidade

O valor retornado por `MobileServiceClient.GetTable` e as consultas são interfaces. Isso os torna fácil de reproduzir para fins de teste, portanto, você pode criar um `MyMockTable : IMobileServiceTable<TodoItem>` que implementa a lógica do teste.

##<a name="customizing"></a>Como personalizar o cliente

Esta seção mostra como pode personalizar os cabeçalhos de solicitação e personalizar a serialização de objetos JSON na resposta.

### <a name="headers"></a>Como personalizar cabeçalhos de solicitação

Para dar suporte ao seu cenário específico de aplicativo, convém personalizar a comunicação com o serviço móvel. Por exemplo, convém adicionar um cabeçalho personalizado para cada solicitação de saída, ou até mesmo alterar códigos de status de respostas. Pode fazer isso fornecendo um DelegatingHandler personalizado, como no exemplo a seguir:

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}

Este código adiciona um novo cabeçalho **x-my-header** na solicitação e define arbitrariamente o código de resposta como indisponível. Num cenário do mundo real, você definiria o código de status de resposta com base em alguma lógica personalizada exigida pelo seu aplicativo.

### <a name="serialization"></a>Como personalizar a serialização

A biblioteca de cliente dos Serviços Móveis usa Json.NET para converter uma resposta JSON em objetos .NET no cliente. Você pode configurar o comportamento dessa serialização entre tipos .NET e JSON nas mensagens. A classe [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expõe uma propriedade `SerializerSettings` do tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Usando essa propriedade, você pode definir uma das muitas propriedades de Json.NET, como as seguintes:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Essa propriedade converte todas as propriedades em letras minúsculas, durante a serialização.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Pesquisar dados por ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Armazenando o token de autenticação em cache]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Adicionar autenticação ao seu aplicativo]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[método LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Tutorial de simultaneidade otimista]: mobile-services-windows-store-dotnet-handle-database-conflicts.md

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Ignorar]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizada nas SDKs do cliente dos Serviços Móveis do Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[InvokeApiSync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx

<!---HONumber=Oct15_HO3-->