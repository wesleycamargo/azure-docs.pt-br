<properties
	pageTitle="Trabalhando com a biblioteca de cliente gerenciado dos Aplicativos Móveis do Serviço de Aplicativo (Windows | Xamarin) | Microsoft Azure"
	description="Saiba como usar um cliente do .NET para os Aplicativos Móveis do Serviço de Aplicativo do Azure com aplicativos do Windows e Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="glenga"/>

# Como usar o cliente gerenciado para Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

##Visão geral

Este guia mostra como executar cenários comuns usando a biblioteca de cliente gerenciado para os Aplicativos Móveis do Serviço de Aplicativo do Azure em aplicativos do Windows e Xamarin. Se você for iniciante nos Aplicativos Móveis, primeiro conclua o tutorial [Início rápido dos Aplicativos Móveis](app-service-mobile-windows-store-dotnet-get-started.md). Neste guia, abordaremos o SDK gerenciado do lado do cliente. Para saber mais sobre os SDKs no servidor para Aplicativos Móveis, confira [Trabalhar com o SDK de back-end do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ou [Como usar o SDK de back-end Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentação de referência

A documentação de referência para o SDK do cliente está localizada aqui: [Referência do cliente do .NET dos Aplicativos Móveis do Azure](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx).

##<a name="setup"></a>Configuração e Pré-requisitos

Supomos que você já criou e publicou o projeto de back-end do Aplicativo Móvel, que inclui uma tabela. No código usado neste tópico, a tabela é denominada `TodoItem` e terá as seguintes colunas: `Id`, `Text` e `Complete`. Essa é a mesma tabela criada quando você conclui o [tutorial de início rápido](app-service-mobile-windows-store-dotnet-get-started.md)

O tipo em C# do lado do cliente tipado correspondente é o seguinte:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Observe que [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) é usado para definir o mapeamento de *PropertyName* entre o tipo de cliente e a tabela.

Para saber como criar novas tabelas em seu back-end de Aplicativos Móveis, confira [Como definir um controlador de tabela](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (back-end do .NET) ou [Definir tabelas usando um esquema dinâmico](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (back-end Node.js). Para um back-end Node.js, você também pode usar a configuração **Tabelas fáceis** no [portal do Azure](https://portal.azure.com/).

##<a name="create-client"></a>Como criar o cliente do Aplicativo Móvel

O código a seguir cria o objeto `MobileServiceClient` que é usado para acessar o back-end do Aplicativo Móvel.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

No código acima, substitua `MOBILE_APP_URL` pela URL do back-end do Aplicativo Móvel, que está localizada na folha de back-end de seu Aplicativo Móvel no [portal do Azure](https://portal.azure.com/).

##<a name="instantiating"></a>Como criar uma referência de tabela

Todos os códigos que acessam e modificam dados em uma tabela de back-end chamam funções no objeto `MobileServiceTable`. Você obtém uma referência à tabela chamando o método [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) em uma instância do `MobileServiceClient`, da seguinte forma:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Esse é o modelo de serialização tipado. Também há suporte para um modelo de serialização não tipado. O exemplo a seguir cria uma referência a uma tabela sem tipo:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Em consultas não tipadas, você deve especificar a cadeia de caracteres de consulta OData subjacente.

##<a name="querying"></a>Como consultar dados do seu Aplicativo Móvel

Esta seção descreve como emitir consultas para o back-end do Aplicativo Móvel, que inclui as seguintes funcionalidades:

- [Filtrar dados retornados]
- [Classificar dados retornados]
- [Retornar dados em páginas]
- [Selecionar colunas específicas]
- [Pesquisar dados por ID]

>[AZURE.NOTE] Um tamanho de página controlado por servidor é usado para impedir que todas as linhas sejam retornadas. Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço. Para retornar mais de 50 linhas, use o método `Take`, conforme descrito em [Retornar dados em páginas].

### <a name="filtering"></a>Como filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula `Where` em uma consulta. Ele retorna todos os itens de `todoTable`, cuja propriedade `Complete` é igual a `false`. A função `Where` aplica um predicado de filtragem de linha à consulta na tabela.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Você pode exibir o URI da solicitação enviado ao back-end usando um software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. Se você examinar o URI abaixo, observe que estamos modificando a própria cadeia de caracteres da consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Essa solicitação normalmente é traduzida aproximadamente para a seguinte consulta SQL no lado do Azure:

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

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável. A opção anterior, de concatenar vários predicados em uma consulta, é mais compacta e recomendada.

A cláusula `where` dá suporte a operações que são convertidas para o subconjunto OData. Isso inclui os operadores relacionais (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisão de número (Math.Floor, Math.Ceiling), funções de cadeias de caracteres (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propriedades de data (Year, Month, Day, Hour, Minute, Second), propriedades de acesso de um objeto e expressões que combinam todos eles.

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

Por padrão, o back-end retorna apenas as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [Take]. Use `Take` juntamente com o método [Ignorar] para solicitar uma "página" específica do conjunto de dados total retornado pela consulta. A consulta a seguir, quando executada, retorna os três itens principais na tabela.

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

>[AZURE.NOTE]Para substituir o limite de 50 linhas em um back-end do Aplicativo Móvel, você também deve aplicar o [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) ao método GET público e especificar o comportamento de paginação. Quando aplicado ao método, o seguinte define o máximo de linhas retornadas para 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Como selecionar colunas específicas

Você pode especificar o conjunto de propriedades para incluir nos resultados, adicionando uma cláusula `Select` à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
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

### <a name="lookingup"></a>Como executar consultas sem tipo

Ao executar uma consulta usando um objeto de tabela sem tipo, você deve especificar expressamente a cadeia de consulta OData, como no seguinte exemplo:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Você recupera valores JSON que podem ser usados como um recipiente de propriedades. Para obter mais informações sobre JToken e Json.NET, consulte [Json.NET](http://json.codeplex.com/)

##<a name="inserting"></a>Como inserir dados em um back-end do Aplicativo Móvel

Todos os tipos de cliente devem conter um membro chamado **Id**, que é por padrão uma cadeia de caracteres. Essa **Id** é necessária para executar operações CRUD e offline. O código a seguir ilustra como inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

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

Os Aplicativos Móveis dão suporte a valores exclusivos e personalizados de cadeia de caracteres para a coluna **id** da tabela. Isso permite que aplicativos usem valores personalizados, como endereços de email ou nomes de usuário para a ID.

IDs de cadeia de caracteres fornecem os seguintes benefícios:

+ As IDs são geradas sem fazer uma viagem ida e volta ao banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de IDs podem integrar-se melhor a uma lógica do aplicativo.

Quando um valor de ID de cadeia de caracteres não está definido em um registro inserido, o back-end do Aplicativo Móvel gera um valor exclusivo para a ID. Você pode usar o método `Guid.NewGuid()` para gerar seus próprios valores de ID, seja no cliente ou no back-end.

##<a name="modifying"></a>Como modificar dados em um back-end de Aplicativo Móvel

O código a seguir ilustra como atualizar uma instância existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

	await todoTable.UpdateAsync(todoItem);

Para inserir dados não tipados, você pode tirar proveito do Json.NET da seguinte maneira:
	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Observe que, ao fazer uma atualização, uma ID deve ser especificada. É assim que o back-end identifica a instância para atualização. A ID pode ser obtida do resultado da chamada de `InsertAsync`. Quando você tenta atualizar um item sem fornecer o valor de “Id”, uma `ArgumentException` é gerada.


##<a name="deleting"></a>Como excluir dados em um back-end do Aplicativo Móvel

O código a seguir ilustra como excluir dados de uma instância existente. A instância é identificada pelo campo "Id" definido em `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para excluir dados não tipados, você pode tirar proveito do Json.NET da seguinte forma:

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Observe que, quando você faz uma solicitação de exclusão, uma ID deve ser especificada. Outras propriedades não são passadas para o serviço ou são ignoradas no serviço. O resultado de uma chamada de `DeleteAsync` geralmente é `null`. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync`. Quando você tenta excluir um item sem o campo “Id” já definido, o back-end retorna uma `MobileServiceInvalidOperationException`.

##<a name="#custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

Você pode chamar uma API personalizada chamando uma das sobrecargas de método [InvokeApiSync] no cliente. Por exemplo, a seguinte linha de código envia uma solicitação POST à API **completeAll** no back-end:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Observe que esta é uma chamada de método tipada, que exige que o tipo de retorno **MarkAllResult** seja definido. Os dois métodos, tipado e não tipado, são aceitos. Este é um exemplo quase trivial, pois é tipado, não envia carga, não possui parâmetros de consulta e não altera os cabeçalhos de solicitação. Para exemplos mais realistas e uma discussão mais abrangente do [InvokeApiAsync], consulte [API personalizada nas SDKs do cliente dos Serviços Móveis do Azure].

##Como registrar para notificações de push

O cliente de Aplicativos Móveis permite que você se registrar para notificações por push com Hubs de Notificação do Azure. Ao se registrar, você obtém um identificador obtido do PNS (Serviço de Notificação por Push) específico da plataforma. Então fornece este valor, juntamente com quaisquer marcas, no momento em que cria o registro. O seguinte código registra seu aplicativo do Windows para notificações de push no WNS (Serviço de Notificação do Windows):

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

Observe que, neste exemplo, são incluídas duas marcas com o registro. Para saber mais sobre os aplicativos do Windows, inclusive como se registrar para obter registros de modelo, confira [Adicionar notificações por push ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md).

Os aplicativos do Xamarin precisam de alguns códigos adicionais para registrar um aplicativo em execução em um aplicativo do iOS ou Android no APNS (Apple Push Notification Service) e nos serviços do GCM (Google Cloud Messaging), respectivamente. Para saber mais, confira **Adicionar notificações por push ao seu aplicativo** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

## Como registrar modelos de envio por push para enviar notificações entre plataformas

Para registrar os modelos, basta passá-los com o método **MobileService.GetPush(). RegisterAsync()** no seu aplicativo cliente.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

Seus modelos serão do tipo JObject e poderão conter vários modelos no seguinte formato JSON:

        public JObject newTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

O método **RegisterAsync()** também aceita Blocos Secundários:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Observe que todas as marcas serão removidas imediatamente por segurança. Para adicionar marcas a instalações ou modelos dentro de instalações, confira [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure].

Para enviar notificações usando esses modelos registrados, trabalhe com as [APIs dos Hubs de Notificação](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="optimisticconcurrency"></a>Como usar simultaneidade otimista

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O *Controle de simultaneidade otimista* pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida.

Os Aplicativos Móveis dão suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item na coluna de propriedades do sistema `__version` definida para cada tabela no back-end do Aplicativo Móvel. Cada vez que um registro é atualizado, os Aplicativos Móveis definem a propriedade `__version` desse registro como um novo valor. Durante cada solicitação de atualização, a propriedade `__version` do registro incluído na solicitação é comparada à mesma propriedade do registro no servidor. Se a versão transmitida com a solicitação não corresponder ao back-end, a biblioteca de cliente gerará um `MobileServicePreconditionFailedException<T>`. O tipo incluído com a exceção é o registro do back-end que contém a versão do registro do servidor. O aplicativo poderá, então, usar essas informações para decidir se deve executar a solicitação de atualização novamente com o valor de `__version` correto do back-end para confirmar as alterações.

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

Para saber mais, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure](app-service-mobile-offline-data-sync.md).


##<a name="binding"></a>Como: associar dados dos Aplicativos Móveis a uma interface do usuário do Windows

Esta seção mostra como exibir os objetos de dados retornados usando elementos da interface do usuário em um aplicativo do Windows. Para consultar itens incompletos em `todoTable` e exibi-los em uma lista muito simples, você pode executar o seguinte código de exemplo para vincular a origem da lista a uma consulta. O uso de `MobileServiceCollection` cria uma coleção de associações com reconhecimento dos Aplicativos Móveis.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Alguns controles no tempo de execução gerenciado dão suporte a uma interface chamada [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Essa interface permite que os controles solicitem dados adicionais quando o usuário rola. Há suporte interno para essa interface para aplicativos universais do Windows 8.1 por meio do `MobileServiceIncrementalLoadingCollection`, que manipula automaticamente as chamadas dos controles. Para usar o `MobileServiceIncrementalLoadingCollection` em aplicativos do Windows, faça o seguinte:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Para usar a nova coleção nos aplicativos do Windows Phone 8 e do “Silverlight”, use os métodos da extensão `ToCollection` em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para efetivamente carregar dados, chame `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Quando usa a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você obtém uma coleção que pode ser vinculada a controles da interface do usuário. Essa coleção tem reconhecimento de paginação, ou seja, um controle pode pedir que a coleção "carregue mais itens", e a coleção fará isso para o controle. Nesse momento não há nenhum código de usuário envolvido, o controle iniciará o fluxo. No entanto, como a coleção está carregando dados da rede, é esperado que haja falhas nesse carregamento algumas vezes. Para lidar com essas falhas, é possível substituir o método `OnException` na `MobileServiceIncrementalLoadingCollection` para tratar das exceções resultantes de chamadas para `LoadMoreItemsAsync` executadas pelos controles.

Finalmente, imagine que sua tabela tenha muitos campos, mas você só deseja exibir alguns deles em seu controle. Você pode usar as diretrizes contidas na seção “[Selecionar colunas específicas](#selecting)” acima a fim de selecionar colunas específicas para exibição na interface do usuário.

## <a name="adal"></a>Como autenticar usuários com a Biblioteca de Autenticação do Active Directory

Você pode usar a ADAL (Biblioteca de autenticação do Active Directory) para conectar os usuários ao seu aplicativo usando o Active Directory do Azure. Normalmente, será melhor usá-la em vez dos métodos `loginAsync()`, pois ela fornece uma aparência mais nativa do UX e permite personalização adicional.

1. Configure o seu back-end de aplicativo móvel para entrada no AAD seguindo o tutorial [Como configurar o Serviço de Aplicativo para o logon do Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Complete a etapa opcional de registrar um aplicativo cliente nativo.

2. No Visual Studio ou Xamarin Studio, abra o projeto e adicione uma referência ao pacote NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Ao pesquisar, inclua versões de pré-lançamento.

3. Adicione o código abaixo ao seu aplicativo, de acordo com a plataforma que você está usando. Em cada um, faça as seguintes substituições:

* Substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário no qual você provisionou o aplicativo. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Active Directory do Azure no [Portal Clássico do Azure].

* Substitua **INSERT-RESOURCE-ID-HERE** pela ID do cliente do seu back-end de aplicativo móvel. Você pode obter isso na guia **Avançadas** em **Configurações do Active Directory do Azure** no portal.

* Substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo cliente nativo.

* Substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade _/.auth/login/done_ do site, usando o esquema HTTPS. Esse valor deve ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

Veja a seguir o código necessário para cada plataforma:

**Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
			}
        }

**Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.SetMessage(ex.Message);
				builder.SetTitle("You must log in. Login Required");
				builder.Create().Show();
			}
        }
		protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
		{
			base.OnActivityResult(requestCode, resultCode, data);
			AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
		}

## <a name="package-sid"></a>Como obter um SID do pacote da Windows Store

Para aplicativos do Windows, um SID de pacote é necessário para habilitar notificações por push. Para obter esse valor:

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, clique em **Armazenar** > **Associar Aplicativo à Store...**.
2. No assistente, clique em **Avançar**, entre com sua conta da Microsoft, digite um nome para seu aplicativo em **Reservar um novo nome de aplicativo** e clique em **Reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar** e em **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
4. Faça logon na [Central de Desenvolvimento do Windows](https://dev.windows.com/pt-BR/overview) usando a sua Conta da Microsoft. Em **Meus aplicativos**, clique no registro de aplicativo que você acabou de criar.
5. Clique em **Gerenciamento de aplicativos** > **Identidade de aplicativos** e, em seguida, role para baixo até encontrar o **SID do Pacote**.

Muitos usos do SID do pacote o tratam como um URI; nesse caso, você precisará usar _ms-app://_ como o esquema. Anote a versão do SID do pacote formado pela concatenação desse valor como um prefixo.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

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

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

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


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](app-service-mobile-how-to-configure-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
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

                // Use the Microsoft account auth token to sign in to App Service.
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


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

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


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>Como tratar erros

O seguinte exemplo mostra como manipular uma exceção que é retornada pelo back-end:

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
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


##<a name="unit-testing"></a>Como criar testes de unidade

O valor retornado por `MobileServiceClient.GetTable` e as consultas são interfaces. Isso os torna fácil de reproduzir para fins de teste, portanto, você pode criar um `MyMockTable : IMobileServiceTable<TodoItem>` que implementa a lógica do teste.

##<a name="customizing"></a>Como personalizar o cliente

Esta seção mostra como pode personalizar os cabeçalhos de solicitação e personalizar a serialização de objetos JSON na resposta.

### <a name="headers"></a>Como personalizar cabeçalhos de solicitação

Para dar suporte ao seu cenário específico de aplicativo, convém personalizar a comunicação com o back-end do Aplicativo Móvel. Por exemplo, convém adicionar um cabeçalho personalizado para cada solicitação de saída, ou até mesmo alterar códigos de status de respostas. Você pode fazer isso fornecendo um [DelegatingHandler] personalizado, como no exemplo a seguir:

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Este código adiciona um novo cabeçalho **x-my-header** na solicitação e define arbitrariamente o código de resposta como indisponível. Num cenário do mundo real, você definiria o código de status de resposta com base em alguma lógica personalizada exigida pelo seu aplicativo.

### <a name="serialization"></a>Como personalizar a serialização

A biblioteca de cliente dos Aplicativos Móveis usa Json.NET para converter uma resposta JSON em objetos .NET no cliente. Você pode configurar o comportamento dessa serialização entre tipos .NET e JSON nas mensagens. A classe [MobileServiceClient] expõe uma propriedade `SerializerSettings` do tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Usando essa propriedade, você pode definir uma das muitas propriedades de Json.NET, como as seguintes:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Essa propriedade converte todas as propriedades em letras minúsculas, durante a serialização.

<!-- Anchors. -->
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Pesquisar dados por ID]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Ignorar]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizada nas SDKs do cliente dos Serviços Móveis do Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[InvokeApiSync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0302_2016-->