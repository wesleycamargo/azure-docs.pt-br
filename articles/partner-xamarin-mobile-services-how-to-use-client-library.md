<properties 
	pageTitle="Como usar o cliente do Componente Xamarin - guia de recursos de serviços móveis do Azure" 
	description="Aprenda a usar o cliente componente Xamarin para os serviços móveis do Azure." 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Como usar a biblioteca de cliente Componente Xamarim para os Serviços Móveis do Azure

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/pt-br/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
  	<a href="/pt-br/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android"></a><a href="/pt-br/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="AndroidXamarin" class="current">Xamarin</a>
</div>


Este guia mostra como executar cenários comuns usando um cliente Componente Xamarim para os Serviços Móveis do Azure, em aplicativos Xamarin para iOS e Android. Os cenários abrangidos incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se você for novo nos serviços móveis, você deverá considerar primeiro concluir o tutorial "Quickstart de serviços móveis" ([Xamarin.iOS][tutorial de início rápido do Xamarin.iOS]/[Xamarin.Android][tutorial de início rápido do Xamarin]) e o tutorial "Introdução com dados no .NET" ([Xamarin.iOS][tutorial de dados Xamarin]/[Xamarin.Android][tutorial de dados Xamarin]). O tutorial Guia de início rápido requer [Xamarin][Download do Xamarin] o [SDK dos Serviços Móveis] e ajuda a configurar sua conta e a criar seu primeiro serviço móvel.


## Sumário

- [O que são Serviços Móveis]
- [Conceitos]
- [Como: Criar o cliente de Serviços Móveis]
- [Como: Criar uma referência de tabela]
- [Como: Consultar dados a partir de um serviço móvel]
	- [Filtrar dados retornados]
    - [Classificar dados retornados]
	- [Retornar dados em páginas]
	- [Selecionar colunas específicas]
	- [Pesquisar dados por ID]
- [Como: Inserir dados em um serviço móvel]
- [Como: Modificar dados em um serviço móvel]
- [Como: Excluir dados em um serviço móvel]
- [Como: Autenticar usuários]
- [Como: Tratar erros]
- [Como: Trabalhar com dados não tipados]
- [Como: Projetar testes da unidade]
- [Próximas etapas]
	
[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Configuração e pré-requisitos</h2>

Suponhamos que você tenha criado um serviço móvel e uma tabela. Para obter mais informações, consulte [Criar uma tabela](http://go.microsoft.com/fwlink/?LinkId=298592). No código usado neste tópico, a tabela é denominada  `TodoItem` e terá as seguintes colunas: `id`, `Text` e `Complete`.

O tipo .NET do lado do cliente tipado correspondente é o seguinte:


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
Quando o esquema dinâmico está habilitado, os Serviços Móveis do Azure geram automaticamente novas colunas com base no objeto de solicitações de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a>Como: Criar o cliente de Serviços Móveis</h2>

O código a seguir cria o objeto  `MobileServiceClient` que é usado para acessar seu serviço móvel. 
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

No código acima, substitua  `AppUrl` e  `AppKey` pela URL e pela chave do aplicativo do serviço móvel, nessa ordem. Ambas estão disponíveis no Portal de Gerenciamento do Azure, selecionando seu serviço móvel e clicando em "Painel".

<h2><a name="instantiating"></a>Como: Criar uma referência de tabela</h2>

Todo código que acessa ou modifica dados na tabela dos Serviços Móveis chama funções no objeto  `MobileServiceTable`. Você obtém uma referência à tabela chamando a função [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) em uma instância do  `MobileServiceClient`. 

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

Esse é o modelo de serialização tipado. Consulte a discussão sobre o <a href="#untyped">modelo de serialização não tipado</a> abaixo.
			
<h2><a name="querying"></a>Como: Consultar dados de um serviço móvel</h2>

Esta seção descreve como emitir consultas para o serviço móvel. As subseções descrevem diferentes aspectos, como classificação, filtragem e paginação. 
			
### <a name="filtering"></a>Como: Filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula  `Where` em uma consulta. Ele retorna todos os itens de  `todoTable` cuja propriedade  `Complete` é igual a  `false`. A função  `Where` aplica um predicado de filtragem de linha à consulta na tabela. 
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Você pode exibir o URI da solicitação enviado ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou Fiddler. Se você examinar o URI abaixo, observe que estamos modificando a própria cadeia de caracteres da consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
Essa solicitação normalmente é traduzida aproximadamente para a seguinte consulta SQL no lado do servidor:
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
A função passada para o método  `Where` pode ter um número arbitrário de condições. Por exemplo, a linha abaixo:

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

A instrução  `where` acima localizará itens com o status  `Complete` definido como falso com  `Text` não nulo.

Em vez disso, também poderíamos ter escrito isso em várias linhas:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável.  A opção anterior -- de concatenar vários predicados em uma consulta - é mais compacta e recomendada.

A cláusula  `where` oferece suporte a operações que são traduzidas para o subconjunto OData dos Serviços Móveis. Isso inclui os operadores relacionais (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisão de número (Math.Floor, Math.Ceiling), funções de cadeias de caracteres (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propriedades de data (Year, Month, Day, Hour, Minute, Second), propriedades de acesso de um objeto e expressões que combinam todos eles.

### <a name="sorting"></a>Como: Classificar dados retornados

O código a seguir ilustra como classificar dados incluindo uma função  `OrderBy` ou  `OrderByDescending` na consulta. Ele retorna os itens da  `todoTable` classificada em ordem crescente pelo campo  `Text`. Por padrão, o servidor retorna apenas os 50 primeiros elementos. 

> [AZURE.NOTE] Um tamanho da página de servidor controlado por nós usado por padrão para prevenir todos os elementos de serem retornados. Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço.

Você pode aumentar o número de itens a ser retornado pela chamada  `Take` conforme descrito na próxima seção.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>Como: Retornar dados em páginas

O código a seguir mostra como implementar a paginação de dados retornados usando as cláusulas  `Take` e  `Skip` na consulta.  A consulta a seguir, quando executada, retorna os três itens principais na tabela. 

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
			
Você também pode usar o método [IncludeTotalCount](http://msdn.microsoft.com/library/windowsazure/jj730933.aspx) para garantir que a consulta obterá a contagem total de <i>todos</i> os registros que seriam retornados, ignorando qualquer cláusula take paging/limit especificada:

	query = query.IncludeTotalCount();

Esse é um cenário simplificado de passagem de valores de paginação embutidos em código para os métodos  `Take` e  `Skip`. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. 

### <a name="selecting"></a>Como: Selecionar colunas específicas

Você pode especificar qual conjunto de propriedades incluir nos resultados adicionando uma cláusula  `Select` à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

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

A função  `LookupAsync` pode ser usada para procurar objetos do banco de dados com uma determinada ID. 

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

<h2><a name="inserting"></a>Como: Inserir dados em um serviço móvel</h2>

> [AZURE.NOTE] Se você desejar executar operações de inserção, pesquisa, exclusão ou atualização em um tipo, será necessário criar um membro chamado **Id** (independentemente do caso). É por isso que a classe de exemplo **TodoItem** tem um membro de nome **Id**. Um valor de ID não deve ser definido como qualquer coisa diferente do valor padrão durante as operações de inserção. Por outro lado, o valor de ID deve sempre ser definido como um valor não padrão e presente nas operações de atualização e exclusão.

O código a seguir ilustra como inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

	await todoTable.InsertAsync(todoItem);

Depois de esperar o retorno da chamada  `todoTable.InsertAsync`, a ID do objeto no servidor é preenchido para o objeto  `todoItem` no cliente. 

Para inserir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado abaixo. Novamente, observe que não deve ser especificada uma ID ao inserir um objeto.

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Se você tentar inserir um item com o campo "Id" já definido, você obterá um  `MobileServiceInvalidOperationException` do serviço. 

<h2><a name="modifying"></a>Como: Modificar dados em um serviço móvel</h2>

O código a seguir ilustra como atualizar uma instância existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

	await todoTable.UpdateAsync(todoItem);


Para inserir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma atualização, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser atualizada. A ID a ser passada pode ser obtida do resultado da chamada de  `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
Se você tentar atualizar um item sem o campo "Id" já estar definido, o serviço não poderá identificar qual instância atualizar e, portanto, você obterá uma  `MobileServiceInvalidOperationException` do serviço. Da mesma forma, se tentar atualizar um item não tipado sem o campo "Id" já definido, você também obterá uma  `MobileServiceInvalidOperationException` do serviço. 
			
			
<h2><a name="deleting"></a>Como: Excluir dados em um serviço móvel</h2>

O código a seguir ilustra como excluir dados de uma instância existente. A instância é identificada pelo campo "Id" definido em  `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para excluir dados não tipados, você pode tirar proveito do Json.NET conforme mostrado a seguir. Observe que quando você faz uma solicitação de exclusão, uma ID deve ser especificada, porque essa é a maneira como o serviço móvel identifica a instância a ser excluída. Uma solicitação de exclusão precisa apenas da ID. Outras propriedades não são passadas para o serviço e, se for passada alguma propriedade, ela será ignorada no serviço. O resultado de uma chamada de  `DeleteAsync`, geralmente, também é  `null`. A ID a ser passada pode ser obtida do resultado da chamada de  `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
Se você tentar excluir um item sem o campo "Id" já estar definido, o serviço não poderá identificar qual instância excluir e, portanto, você obterá uma  `MobileServiceInvalidOperationException` do serviço. Da mesma forma, se tentar excluir um item não tipado sem o campo "Id" já definido, você também obterá uma  `MobileServiceInvalidOperationException` do serviço. 
		


<h2><a name="authentication"></a>Como: Autenticar usuários</h2>

Os Serviços Móveis dão suporte à autenticação e à autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e Active Directory do Azure. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial "Introdução à autenticação ([Xamarin.iOS][autenticação do Xamarin.iOS][/Xamarin.Android][autenticação do Xamarin.Android]).

Dois fluxos de autenticação têm suporte: um _server flow_ e um _client flow_. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos ao dispositivo pois depende dos SDKs específicos ao provedor e ao dispositivo.

<h3>Fluxo de servidor</h3>
Para que os Serviços Móveis gerenciem o processo de autorização em seu aplicativo da Windows Store ou do Windows Phone, você deve registrar seu aplicativo com o provedor de identidade. Em seguida, no seu serviço móvel, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial "Introdução à autenticação ([Xamarin.iOS][autenticação do Xamarin.iOS][/Xamarin.Android][autenticação do Xamarin.Android]).

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

<h3><a name="caching"></a>Armazenando o token de autenticação em cache</h3>
Em alguns casos, a chamada para o método de logon pode ser evitada após a primeira vez que o usuário é autenticado. Você pode usar um armazenamento seguro local (como[Xamarin.Auth][componente Xamarin.Auth]) para armazenar a identidade do usuário atual em cache na primeira vez que ele fizer logon e em todas as vezes subsequentes que verificar se você já tem a identidade do usuário em seu cache. Quando o cache estiver vazio, ainda será necessário enviar o usuário pelo processo de logon. 

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
	accountStore.Delete(account, "Facebook");


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

O cliente Componente Xamarin foi desenvolvido para cenários fortemente tipados. No entanto, às vezes, uma experiência menos rígida é conveniente, por exemplo, ao lidar com objetos com esquema aberto. Esse cenário é habilitado da seguinte forma. Em consultas, você abandona o LINQ e usa o formato de conexão.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Você recupera valores JSON que podem ser usados como um recipiente de propriedades. Para obter mais informações sobre JToken e Json.NET, consulte [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Como: Projetar testes da unidade</h2>

O valor retornado por  `MobileServiceClient.GetTable` e as consultas são interfaces. Isso os torna fácil de reproduzir para fins de teste, portanto, você pode criar um  `MyMockTable : IMobileServiceTable<TodoItem>` que implementa a lógica do teste.

<h2><a name="nextsteps"></a>Próximas etapas</h2>

Agora que você concluiu as instruções deste tópico de referência conceitual, saiba como executar tarefas importantes nos Serviços Móveis em detalhes:

* Introdução aos Serviços Móveis ([Xamarin.iOS][Introdução aos Serviços Móveis iOS][/Xamarin.Android][Introdução aos Serviços Móveis Android])
  <br/>Aprenda os conceitos básicos de como usar os Serviços Móveis.

* Introdução aos dados ([Xamarin.iOS][Introdução aos dados iOS][/Xamarin.Android][Introdução aos dados Android])
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* Introdução à autenticação([Xamarin.iOS][Introdução à autenticação iOS][/Xamarin.Android][Introdução à autenticação Android])
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* Validar e modificar dados com scripts ([Xamarin.iOS][Validar e modificar dados com scripts iOS][/Xamarin.Android][Validar e modificar dados com scripts Android])
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* Refinar consultas com paginação ([Xamarin.iOS][Refinar consultas com paginação iOS][/Xamarin.Android][Refinar consultas com paginação Android])
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

* Autorizar usuários com scripts ([Xamarin.iOS][Autorizar usuários com scripts iOS][/Xamarin.Android][Autorizar usuários com scripts Android])
  <br/>Saiba como usar o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usá-lo para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Como: Criar o cliente de Serviços Móveis]: #create-client
[Como: Criar uma referência de tabela]: #instantiating
[Como: Consultar dados a partir de um serviço móvel]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Pesquisar dados por ID]: #lookingup
[Como: Vincular dados à interface do usuário em um serviço móvel]: #binding
[Como: Inserir dados em um serviço móvel]: #inserting
[Como: Modificar dados em um serviço móvel]: #modifying
[Como: Excluir dados em um serviço móvel]: #deleting
[Como: Autenticar usuários]: #authentication
[Como: Tratar erros]: #errors
[Como: Projetar testes da unidade]: #unit-testing 
[Como: Consultar dados a partir de um serviço móvel]: #querying
[Como: Personalizar o cliente]: #customizing
[Como: Trabalhar com dados não tipados]: #untyped
[Personalizar cabeçalhos de solicitação]: #headers
[Personalizar a serialização]: #serialization
[Próximas etapas]: #nextsteps
[Armazenando o token de autenticação em cache]: #caching

<!-- URLs. -->
[Introdução aos Serviços Móveis iOS]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos Serviços Móveis Android]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
[Download do Xamarin]: http://xamarin.com/download/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Tutorial de início rápido do Xamarin.iOS]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios/
[Tutorial de início rápido do Xamarin.Android]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android/
[Tutorial de dados do Xamarin.iOS]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Tutorial de dados do Xamarin.Android]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Autenticação do Xamarin.iOS]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Autenticação do Xamarin.Android]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Componente Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth

[SDK dos Serviços Móveis]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Introdução aos dados iOS]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução aos dados Android]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à autenticação iOS]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução à autenticação Android]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validar e modificar dados com scripts iOS]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validar e modificar dados com scripts Android]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refinar consultas com paginação iOS]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Refinar consultas com paginação Android]: /pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Autorizar usuários com scripts iOS]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Autorizar usuários com scripts Android]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[Método LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx



<!--HONumber=42-->
