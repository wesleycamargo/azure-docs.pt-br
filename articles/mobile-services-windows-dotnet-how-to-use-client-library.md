<properties 
	pageTitle="Trabalhando com a biblioteca de cliente do .NET para Serviços Móveis" 
	description="Aprenda a usar um cliente .NET para serviços móveis do Azure." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="glenga"/>





# Como usar um cliente .NET para os Serviços Móveis do Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/en-us/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/en-us/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/en-us/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Este guia mostra como executar cenários comuns usando um cliente .NET para os Serviços Móveis do Azure, em aplicativos da Windows Store e em aplicativos do Windows Phone. Os cenários abrangidos incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se você for novo nos Serviços Móveis, deverá considerar primeiro concluir o tutorial "Início rápido dos Serviços Móveis " ([Tutorial de início rápido da Windows Store]/[Tutorial de início rápido do Windows Phone]) e o tutorial "Introdução aos dados no .NET" ([Tutorial de dados da Windows Store]/[Tutorial de dados do Windows Phone]). O tutorial de início rápido requer o[SDK dos Serviços Móveis] e ajuda a configurar sua conta e a criar seu primeiro serviço móvel.


## Sumário

- [O que são Serviços Móveis]
- [Conceitos]
- [Como: Criar o cliente de Serviços Móveis]
- [Como: Criar uma referência de tabela]
- [Como: Consultar dados por meio de um serviço móvel]
	- [Filtrar dados retornados]
    - [Classificar dados retornados]
	- [Retornar dados em páginas]
	- [Selecionar colunas específicas]
	- [Pesquisar dados por ID]
- [Como: Inserir dados em um serviço móvel]
- [Como: Modificar dados em um serviço móvel]
- [Como: Excluir dados em um serviço móvel]
- [Como: Chamar uma API personalizada]
- [Como: Usar Simultaneidade Otimista]
- [Como: Vincular dados à interface do usuário em um serviço móvel]
- [Como: Autenticar usuários]
- [Como: Tratar erros]
- [Como: Trabalhar com dados não tipados]
- [Como: Projetar testes da unidade]
- [Como: Personalizar o cliente]
	- [Personalizar cabeçalhos de solicitação]
	- [Personalizar a serialização]
- [Próximas etapas]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Configuração e pré-requisitos</h2>

Suponhamos que você tenha criado um serviço móvel e uma tabela. Para obter mais informações, consulte [Criar uma tabela](http://go.microsoft.com/fwlink/?LinkId=298592). No código usado neste tópico, a tabela é denominada `TodoItem` e terá as seguintes colunas: `Id`, `Text` e `Complete`.

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

<h2><a name="create-client"></a>Como: Criar o cliente de Serviços Móveis</h2>

O código a seguir cria o objeto `MobileServiceClient` que é usado para acessar seu serviço móvel.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

No código acima, substitua `AppUrl` e `AppKey` pela URL e pela chave do aplicativo do serviço móvel, nessa ordem. Ambas estão disponíveis no Portal de Gerenciamento do Azure, selecionando seu serviço móvel e clicando em "Painel".

<h2><a name="instantiating"></a>Como: Criar uma referência de tabela</h2>

Todo código que acessa ou modifica dados na tabela dos Serviços Móveis chama funções no objeto `MobileServiceTable`. Você obtém uma referência à tabela chamando a função [GetTable](http://msdn.microsoft.com/en-us/library/windowsazure/jj554275.aspx) em uma instância do `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Esse é o modelo de serialização tipado; consulte a discussão sobre o <a href="#untyped">modelo de serialização não tipado</a> abaixo.

<h2><a name="querying"></a>Como: Consultar dados por meio de um serviço móvel</h2>

Esta seção descreve como emitir consultas para o serviço móvel. As subseções descrevem diferentes aspectos, como classificação, filtragem e paginação.

>[AZURE.NOTE] Por padrão, um tamanho de página controlado por servidor é usado para impedir que todas as linhas sejam retornadas. Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço. Para retornar mais de 50 linhas, use o método `Take`, conforme descrito em [Retornar dados em páginas].  

### <a name="filtering"></a>Como: Filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula `Where` em uma consulta. Ele retorna todos os itens de `todoTable` cuja propriedade `Complete` é igual a `false`. A função `Where` aplica um predicado de filtragem de linha à consulta na tabela.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Você pode exibir o URI da solicitação enviado ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou [Fiddler]. Se você examinar o URI abaixo, observe que estamos modificando a própria cadeia de caracteres da consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Essa solicitação normalmente é traduzida aproximadamente para a seguinte consulta SQL no lado do servidor:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

A função que é passada para o método `Where` pode ter um número arbitrário de condições. Por exemplo, a linha abaixo:

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

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável.  A opção anterior -- de concatenar vários predicados em uma consulta - é mais compacta e recomendada.

A cláusula `where` oferece suporte a operações que são traduzidas para o subconjunto OData dos Serviços Móveis. Isso inclui os operadores relacionais (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisão de número (Math.Floor, Math.Ceiling), funções de cadeias de caracteres (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propriedades de data (Year, Month, Day, Hour, Minute, Second), propriedades de acesso de um objeto e expressões que combinam todos eles.

### <a name="sorting"></a>Como: Classificar dados retornados

O código a seguir ilustra como classificar dados incluindo uma função `OrderBy` ou `OrderByDescending` na consulta. Ele retorna itens de `todoTable` classificados em ordem crescente pelo campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Como: Retornar dados em páginas

Por padrão, o servidor retorna apenas as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [Take]. Use `Take` juntamente com o método [Skip] para solicitar uma "página" específica do conjunto de dados total retornado pela consulta. A consulta a seguir, quando executada, retorna os três itens principais na tabela.

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

Você também pode usar o método [IncludeTotalCount] para garantir que a consulta obterá a contagem total de <i>todos</i> os registros que seriam retornados, ignorando qualquer cláusula take paging/limit especificada:

	query = query.IncludeTotalCount();

Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos `Take` e `Skip`. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores.

### <a name="selecting"></a>Como: Selecionar colunas específicas

Você pode especificar qual conjunto de propriedades incluir nos resultados adicionando uma cláusula `Select` à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

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

### <a name="lookingup"></a>Como: Pesquisar dados por ID

A função `LookupAsync` pode ser usada para procurar objetos do banco de dados com uma determinada ID.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>Como: Inserir dados em um serviço móvel

> [AZURE.NOTE] Se você desejar executar operações de inserção, pesquisa, exclusão ou atualização em um tipo, será necessário criar um membro chamado **Id**. É por isso que a classe de exemplo **TodoItem** tem um membro de nome **Id**. Um valor de id válido deve sempre estar presente em operações de atualização e exclusão.

O código a seguir ilustra como inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

	await todoTable.InsertAsync(todoItem);

Se um valor exclusivo de ID personalizada não estiver incluído no `todoItem` passado para a chamada `todoTable.InsertAsync`, um valor de ID será gerado pelo servidor definido no objeto `todoItem` retornado para o cliente.

Os Serviços Móveis oferecem suporte a valores exclusivos e personalizados de cadeia de caracteres para a ID da tabela. Isso permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário, para a coluna de ID de uma tabela dos Serviços Móveis. Se um valor de id de cadeia de caracteres não for fornecido ao inserir novos registros em uma tabela, os Serviços Móveis gerarão um valor exclusivo para a id.

O suporte às ids de cadeia de caracteres oferece as seguintes vantagens aos desenvolvedores

+ Os Ids podem ser gerados sem fazer uma varredura no banco de dados.
+ Os registros são mais fáceis de mesclar em tabelas ou bancos de dados diferentes.
+ Os valores de ids podem integrar-se melhor a uma lógica do aplicativo.

Você também pode usar scripts de servidor para definir valores de ids. O exemplo de script a seguir gera um GUID personalizado e o atribui a um novo id de registro. Isso é semelhante ao valor de id que os Serviços Móveis gerariam se você não transmitisse um valor para um id de registro.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Se um aplicativo fornecer um valor para uma id, os Serviços Móveis irão armazená-lo como está. Isso inclui espaços em branco à direita ou à esquerda. O espaço em branco não será cortado do valor.

O valor da `id` deve ser exclusivo e não deve incluir caracteres dos seguintes conjuntos:

+ Caracteres de controle: [0x0000-0x001F] e [0x007F-0x009F]. Para obter mais informações, consulte [Códigos de controle ASCII C0 e C1].
+  Caracteres de impressão: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Os ids "." e ".."


Como alternativa, você pode usar Ids de números inteiros para suas tabelas. Para usar uma Id de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId`. Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas de Serviços Móveis].


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


<h2><a name="modifying"></a>Como: Modificar dados em um serviço móvel</h2>

O código a seguir ilustra como atualizar uma instância existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

	await todoTable.UpdateAsync(todoItem);


Para inserir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma atualização, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser atualizada. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Se você tentar atualizar um item sem fornecer o valor da "Id", o serviço não poderá identificar a instância a ser atualizada e, portanto, o SDK dos Serviços Móveis gerará uma `ArgumentException`.


<h2><a name="deleting"></a>Como: Excluir dados em um serviço móvel</h2>

O código a seguir ilustra como excluir dados de uma instância existente. A instância é identificada pelo campo "Id" definido em `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para excluir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma solicitação de exclusão, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser excluída. Uma solicitação de exclusão precisa apenas da ID. Outras propriedades não são passadas para o serviço e, se for passada alguma propriedade, ela será ignorada no serviço. O resultado de uma chamada de `DeleteAsync`, geralmente, também é `null`. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Se você tentar excluir um item sem o campo "Id" já estar definido, o serviço não poderá identificar qual instância excluir e, portanto, você obterá uma `MobileServiceInvalidOperationException` do serviço. Da mesma forma, se tentar excluir um item não tipado sem o campo "Id" já definido, você também obterá uma `MobileServiceInvalidOperationException` do serviço.

##<a name="#custom-api"></a>Como: Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON. Para um exemplo completo, incluindo como criar uma API personalizada em seu serviço móvel, consulte [Chamar uma API personalizada do cliente].

Você pode chamar uma API personalizada chamando uma das sobrecargas de método [InvokeApiAsync] no cliente. Por exemplo, a linha de código a seguir envia uma solicitação POST à API **completeAll** no serviço móvel:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Observe que esta é uma chamada de método tipada, que exige que o tipo de retorno **MarkAllResult** seja definido. Os dois métodos, tipado e não tipado, são aceitos. Este é um exemplo quase trivial, pois é tipado, não envia carga, não possui parâmetros de consulta e não altera os cabeçalhos de solicitação. Para exemplos mais realistas e uma discussão mais abrangente do [InvokeApiAsync], consulte [API personalizada nas SDKs do cliente dos Serviços Móveis do Azure].


##<a name="optimisticconcurrency"></a>Como: Usar simultaneidade otimista

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O Controle de Simultaneidade Otimista pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida.

Os Serviços Móveis oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema __version que é definida para cada tabela criada pelos Serviços Móveis. Cada vez que um registro é atualizado, os Serviços Móveis definem a propriedade __version desse registro como um novo valor. Durante cada solicitação de atualização, a propriedade __version do registro incluído na solicitação é comparada à mesma propriedade do registro no servidor. Se a versão passada com a solicitação não coincide com o servidor, a biblioteca de cliente .NET dos serviços móveis gera uma `MobileServicePreconditionFailedException<T>`. O tipo incluído com a exceção é o registro do servidor que contém a versão do registro encontrada no servidor. O aplicativo pode usar essas informações para decidir se deve executar a solicitação de atualização novamente com o valor __version correto do servidor para confirmar as alterações.  

Para habilitar a simultaneidade otimista, o aplicativo define uma coluna na classe da tabela para a propriedade do sistema __version. A definição a seguir fornece um exemplo.

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


O código a seguir mostra como resolver um conflito de gravação quando detectado. O valor correto de __version deve ser incluído na chamada de `UpdateAsync()` para confirmar uma resolução.

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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


<h2><a name="binding"></a>Como: Vincular dados à interface do usuário em um serviço móvel</h2>

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

Alguns controles no Tempo de Execução do Windows oferecem suporte a uma interface chamada [ISupportIncrementalLoading (a página pode estar em inglês)](http://msdn.microsoft.com/en-us/library/windows/apps/Hh701916). Essa interface permite que os controles solicitem dados adicionais quando o usuário rola. Há suporte interno para essa interface para aplicativos da Windows Store via `MobileServiceIncrementalLoadingCollection`, que manipula automaticamente as chamadas dos controles. Para usar `MobileServiceIncrementalLoadingCollection` em aplicativos da Windows Store, proceda da seguinte maneira:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Para usar a nova coleção no Windows Phone, use os métodos de extensão `ToCollection` em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para efetivamente carregar dados, chame `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Quando usa a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você obtém uma coleção que pode ser vinculada a controles da interface do usuário. Essa coleção tem reconhecimento de paginação, ou seja, um controle pode pedir que a coleção "carregue mais itens", e a coleção fará isso para o controle. Nesse momento não há nenhum código de usuário envolvido, o controle iniciará o fluxo. No entanto, como a coleção está carregando dados da rede, é esperado que haja falhas nesse carregamento algumas vezes. Para lidar com essas falhas, é possível substituir o método `OnException` na `MobileServiceIncrementalLoadingCollection` para tratar das exceções resultantes de chamadas para `LoadMoreItemsAsync` executadas pelos controles.

Finalmente, imagine que sua tabela tenha muitos campos, mas você só deseja exibir alguns deles em seu controle. Você pode usar as diretrizes da seção <a href="#selecting">"Selecionar colunas específicas"</a> acima para selecionar colunas específicas a serem exibidas na interface do usuário.

<h2><a name="authentication"></a>Como: Autenticar usuários</h2>

Os Serviços Móveis dão suporte à autenticação e à autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e Active Directory do Azure. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial "Introdução à autenticação" ([da Windows Store][autenticação da Windows Store]/[Windows Phone][autenticação do Windows Phone]).

Dois fluxos de autenticação são suportados: um _server flow_ e um _client flow_. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos ao dispositivo pois depende dos SDKs específicos ao provedor e ao dispositivo.

<h3>Fluxo de servidor</h3>
Para que Serviços Móveis gerencie o processo de autenticação em seu aplicativo Windows Store ou Windows Phone,
você deve registrar seu aplicativo com seu provedor de identidade. Em seguida, no seu serviço móvel, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial "Introdução à autenticação" ([da Windows Store][autenticação da Windows Store]/[Windows Phone][autenticação do Windows Phone]).

Depois de registrar o provedor de identidade, basta chamar o [método LoginAsync] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, o código a seguir inicia um logon de fluxo de servidor usando o Facebook.

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

Nesse caso, os Serviços Móveis gerenciam o fluxo de autenticação OAuth 2.0 exibindo a página de logon do provedor selecionado e gerando um token de autenticação dos Serviços Móveis depois de um logon bem-sucedido com o provedor de identidade. O [método LoginAsync] retorna um [MobileServiceUser], que fornece a [userId] do usuário autenticado e o [MobileServiceAuthenticationToken] como um JWT (token da web JSON). Esse token pode ser armazenado em cache e reutilizado até que expire. Para obter mais informações, consulte [Armazenando o token de autenticação em cache].

> [AZURE.NOTE] **Aplicativo da Windows Store**
Ao usar o provedor de logon da Conta da Microsoft para autenticar usuários de seu aplicativo da Windows Store, você também deve registrar o pacote do aplicativo nos Serviços Móveis. Ao registrar as informações do pacote do aplicativo da Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de logon da conta da Microsoft para obter uma experiência de logon único. Se você não fizer isso, os usuários de logon da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Para saber como registrar seu pacote de aplicativos da Windows Store, consulte [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Depois que as informações do pacote estiverem registradas nos Serviços Móveis, chame o método LoginAsync fornecendo um valor **verdadeiro** para o parâmetro useSingleSignOn para reutilizar as credenciais.

<h3>Fluxo de cliente</h3>

Seu aplicativo também pode entrar em contato independentemente com o provedor de identidade e fornecer o token retornado aos Serviços Móveis para autenticação. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade.

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

Ao usar uma conta da Microsoft, faça logon da seguinte maneira:

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Para obter um exemplo de como usar a Conta da Microsoft para fornecer uma experiência de logon único, consulte "Autenticar seu aplicativo com logon único ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

<h3><a name="caching"></a>Armazenando o token de autenticação em cache</h3>
Em alguns casos, a chamada para o método de logon pode ser evitada após a primeira vez que o usuário é autenticado. Você pode usar o [PasswordVault] para aplicativos da Windows Store para armazenar a identidade do usuário atual em cache na primeira vez que fizer logon e em todas as vezes subsequentes em que você verificar se já tem a identidade do usuário em nosso cache. Quando o cache estiver vazio, ainda será necessário enviar o usuário pelo processo de logon.

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

<h2><a name="errors"></a>Como: Tratar erros</h2>

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

Esse script do lado do servidor valida o tamanho dos dados da cadeia de caracteres enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

Agora que o serviço móvel está validando dados e enviando respostas de erros no lado do servidor, você pode atualizar seu aplicativo .NET para poder tratar de respostas de erros de validação.

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

<h2><a name="untyped"></a>Como: Trabalhar com dados não tipados</h2>

O cliente .NET foi desenvolvido para cenários fortemente tipados. No entanto, às vezes, uma experiência menos rígida é conveniente, por exemplo, ao lidar com objetos com esquema aberto. Esse cenário é habilitado da seguinte forma. Em consultas, você abandona o LINQ e usa o formato de conexão.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Você recupera valores JSON que podem ser usados como um recipiente de propriedades. Para obter mais informações sobre JToken e Json.NET, consulte [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Como: Projetar testes da unidade</h2>

O valor retornado por `MobileServiceClient.GetTable` e as consultas são interfaces. Isso os torna fácil de reproduzir para fins de teste, portanto, você pode criar um `MyMockTable : IMobileServiceTable<TodoItem>` que implementa sua lógica de teste.

<h2><a name="customizing"></a>Como: Personalizar o cliente</h2>

### <a name="headers"></a>Como: Personalizar cabeçalhos de solicitação

Você pode desejar anexar um cabeçalho personalizado para cada solicitação de saída ou alterar os códigos de status de respostas. Você pode fazer isso configurando um DelegatingHandler, como abaixo:

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


### <a name="serialization"></a>Como: Personalizar a serialização

A classe [MobileServiceClient](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expõe uma propriedade `SerializerSettings` do tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Usando essa propriedade, você pode definir propriedades Json.NET (há muitas), incluindo uma, por exemplo, para converter todas as propriedades em letras minúsculas:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>Próximas etapas</h2>

Agora que você concluiu as instruções deste tópico de referência conceitual, saiba como executar tarefas importantes nos Serviços Móveis em detalhes:

* [Introdução aos Serviços Móveis]
  <br/>Aprenda os conceitos básicos de como usar os Serviços Móveis.

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

* [Autorizar usuários com scripts]
  <br/>Saiba como usar o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usá-lo para filtrar os dados retornados pelos Serviços Móveis.

<!-- Anchors. -->
[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Como: Criar o cliente de Serviços Móveis]: #create-client
[Como: Criar uma referência de tabela]: #instantiating
[Como: Consultar dados por meio de um serviço móvel]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Pesquisar dados por ID]: #lookingup
[Como: Vincular dados à interface do usuário em um serviço móvel]: #binding
[Como: Inserir dados em um serviço móvel]: #inserting
[Como: Modificar dados em um serviço móvel]: #modifying
[Como: Excluir dados em um serviço móvel]: #deleting
[Como: Usar Simultaneidade Otimista]: #optimisticconcurrency
[Como: Autenticar usuários]: #authentication
[Como: Tratar erros]: #errors
[Como: Projetar testes da unidade]: #unit-testing
[Como: Consultar dados por meio de um serviço móvel]: #querying
[Como: Personalizar o cliente]: #customizing
[Como: Trabalhar com dados não tipados]: #untyped
[Personalizar cabeçalhos de solicitação]: #headers
[Personalizar a serialização]: #serialization
[Próximas etapas]: #nextsteps
[Armazenando o token de autenticação em cache]: #caching
[Como: Chamar uma API personalizada]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Tutorial de início rápido da Windows Store]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/
[Tutorial de início rápido do Windows Phone]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-wp8/
[Tutorial de dados da Windows Store]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/
[Tutorial de dados do Windows Phone]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-wp8/
[Autenticação da Windows Store]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Autenticação do Windows Phone]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/en-us/library/windows/apps/windows.security.credentials.passwordvault.aspx
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/en-us/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[SDK dos Serviços Móveis]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet/
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Validar e modificar dados com scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Autorizar usuários com scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Método LoginAsync]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI para gerenciar tabelas dos Serviços Móveis]: http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Tutorial de simultaneidade otimista]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/en-us/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/en-us/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/en-us/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizada nos SDKs de cliente de Serviços Móveis do Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Chamar uma API personalizada do cliente]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
\n<!--HONumber=42-->
