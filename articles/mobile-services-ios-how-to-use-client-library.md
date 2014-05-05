<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="Biblioteca de cliente iOS" pageTitle="Como usar a biblioteca de cliente iOS - Serviços Móveis do Azure" metaKeywords="Serviços Móveis do Azure, biblioteca de cliente iOS para Serviços Móveis, biblioteca de cliente iOS" description="Saiba como usar a biblioteca de cliente iOS para os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Como usar a biblioteca de cliente iOS para os Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />




# Como usar a biblioteca de cliente do iOS para os Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/pt-br/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Este guia mostra como executar cenários comuns usando o cliente iOS para os Serviços Móveis do Azure. As amostras são escritas em objective-C e exigem o [SDK dos Serviços Móveis].  Este tutorial também exige o [SDK do iOS]. Os cenários abordados incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se for novo nos Serviços Móveis, você deve primeiro concluir o [início rápido dos Serviços Móveis][Get started with Mobile Services]. O tutorial de início rápido ajuda a configurar sua conta e criar seu primeiro serviço móvel.

## Sumário

- [O que são Serviços Móveis][]
- [Conceitos][]
- [Configuração e pré-requisitos][]
- [Como: Criar o cliente dos Serviços Móveis][]
- [Como: criar uma referência de tabela][]
- [Como: consultar dados de um serviço móvel][]
	- [Filtrar dados retornados]
    - [Usando o objeto MSQuery][How to: Use MSQuery]
	- [Selecionar colunas específicas]
- [Como: inserir dados em um serviço móvel]
- [Como modificar dados em um serviço móvel]
- [Como: associar dados à interface do usuário]
- [Como: autenticar usuários]
- [Como: tratar erros]

<!--- [How to: Design unit tests]
- [How to: Customize the client]
	- [Customize request headers]
	- [Customize data type serialization]
- [Next steps][]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Configuração e pré-requisitos

Este guia pressupõe que você tenha criado um serviço móvel com uma tabela.  Para obter mais informações, consulte [Criar uma tabela]. Os exemplos deste tópico usam uma tabela denominada `ToDoItem`, que tem as seguintes colunas:

+ `ID`
+ `text`
+ `concluído`
+ `duration`


Se você estiver criando seu aplicativo iOS pela primeira vez, certifique-se de adicionar o `WindowsAzureMobileServices.framework` na configuração **Vincular binário com bibliotecas** do seu aplicativo.

Além disso, você deve adicionar a referência a seguir aos arquivos apropriados ou ao arquivo .pch do seu aplicativo.

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a><span class="short-header">Criar o cliente</span>Como: criar o cliente dos Serviços Móveis</h2>

O código a seguir cria o objeto de cliente de serviços móveis que é usado para acessar seu serviço móvel. 

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]
	
No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL do serviço móvel e pela chave de aplicativo do seu serviço móvel. Para determinar essas configurações para o seu serviço móvel, no Portal de Gerenciamento do Azure, selecione seu serviço móvel e clique em **Painel**.

Você também pode criar o cliente de um objeto **NSURL** que é a URL do serviço, da seguinte maneira:

	MSClient *client = [MSClient clientWithApplicationURL:(NSURL *)url
								 applicationKey:(NSString *)string];

<h2><a name="table-reference"></a><span class="short-header">Criar uma referência de tabela</span>Como: criar uma referência de tabela</h2>

Antes de acessar os dados do seu serviço móvel, você deve obter uma referência à tabela da qual deseja consultar, atualizar ou excluir itens. No exemplo a seguir, `ToDoItem` é o nome da tabela:

	MSTable *table = [client tableWithName:@"ToDoItem"]; 


<h2><a name="querying"></a><span class="short-header">Consultar dados</span>Como: consultar dados de um serviço móvel</h2>

Depois que tiver um objeto MSTable, você poderá criar sua consulta.  A consulta simples a seguir obtém todos os itens da tabela ToDoItem.

	[table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

Observe que, neste caso, simplesmente gravamos o texto da tarefa no log.

Os parâmetros a seguir são fornecidos no retorno de chamada:

+ _items_: um **NSArray** dos registros que corresponderam à sua consulta.
+ _totalCount_: a contagem total dos itens em todas as páginas da consulta, e não apenas os retornados na página atual. Esse valor é definido como -1, a menos que você solicite explicitamente a contagem total na sua solicitação. Para obter mais informações, consulte [Retornar dados nas páginas].
+ _error_: qualquer erro ocorrido; caso contrário, `nil`.

### <a name="filtering"></a>Como filtrar dados retornados

Quando você quiser filtrar os resultados, haverá várias opções disponíveis. 

O caso mais comum é o uso de um NSPredicate para filtrar os resultados.

	[table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];
	
O predicado a seguir retorna apenas os itens incompletos na nossa tabela ToDoItem:

	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	[table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//loop through our results
	}];
	
Um único registro pode ser recuperado usando sua ID.

	[table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
		//your code here
	}];

Observe que, nesse caso, os parâmetros de retorno de chamada são ligeiramente diferentes.  Em vez de obter uma matriz de resultados e uma contagem opcional, você obtém apenas o único registro.

### <a name="query-object"></a>Usando o objeto MSQuery

Use o objeto **MSQuery** quando precisar de uma consulta mais complexa do que apenas a filtragem de linhas, como alterar a ordem de classificação nos seus resultados ou limitar o número de registros de dados retornados. Os dois exemplos a seguir mostram como criar uma instância do objeto MSQuery:

+	`MSQuery *query = [table query];`	
+	`MSQuery *query = [table queryWithPredicate:(NSPredicate *)predicate];`

O objeto MSQuery permite que você controle os comportamentos de consulta a seguir:

* Especificar a ordem de retorno dos resultados.
* Limitar quais campos são retornados.
* Limitar quantos registros são retornados.
* Especificar se será incluída a contagem total na resposta.
* Especificar parâmetros de cadeia de caracteres de consulta personalizados na solicitação.

Para definir ainda mais uma consulta, aplique uma ou mais funções. Quando a consulta estiver definida, ela será executada, chamando a função **readWithCompletion**.

#### <a name="sorting"></a>Classificando os dados retornados

As funções a seguir são usadas para especificar os campos usados para classificação:

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field
	
Esta consulta classifica primeiro os resultados pela duração e, em seguida, pela conclusão ou não da tarefa:

	[query orderByAscending(@"duration")];
	[query orderByAscending(@"complete")];
	[query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];	

#### <a name="paging"></a>Retornando dados nas páginas 

Os Serviços Móveis limitam a quantidade de registros retornados em uma única resposta. Para controlar o número de registros exibidos aos usuários, você deve implementar um sistema de paginação.  A paginação é executada usando as três propriedades a seguir do objeto **MSQuery**:

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`

No exemplo a seguir, uma função simples solicita 20 registros do servidor e, em seguida, acrescenta-os à coleção local dos registros carregados anteriormente:

	- (bool) loadResults() {
		MSQuery *query = [self.table query];

		query.includeTotalCount = YES;
		query.fetchLimit = 20;
		query.fetchOffset = self.loadedItems.count;
		[query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
			if(!error) {
				//add the items to our local copy
				[self.loadedItems addObjectsFromArray:items];		

				//set a flag to keep track if there are any additional records we need to load
				self.moreResults = (self.loadedItems.count < totalCount);
			}
		}];
	}

#### <a name="selecting"></a>Limitando os campos retornados

Para limitar quais campos serão retornados da sua consulta, basta especificar os nomes dos campos desejados na propriedade **selectFields**. O exemplo a seguir retorna somente o texto e os campos preenchidos:

	query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Especificando parâmetros de cadeia de caracteres de consulta adicionais

A biblioteca de cliente permite incluir parâmetros de cadeia de caracteres de consulta adicionais na solicitação ao servidor. Esses parâmetros podem ser exigidos pelo seus scripts do lado do servidor. O exemplo a seguir adiciona dois parâmetros de cadeia de caracteres de consulta à solicitação:   

	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};

Esses parâmetros são acrescentados ao URI de consulta como `myKey1=value1&myKey2=value2`. 
Para obter mais informações, consulte [Como: acessar parâmetros personalizados].

<h2><a name="inserting"></a><span class="short-header">Inserindo dados</span>Como inserir dados em um serviço móvel</h2>

Para inserir uma nova linha na tabela, crie um novo [objeto NSDictionary] e passe-o para a função de inserção. O código a seguir insere um novo item todo na tabela:

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified 
		// data compared to what was passed to the server.
	}];	

Os Serviços Móveis oferecem suporte a valores de cadeia de caracteres exclusivos e personalizados para a id da tabela. Isso permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário, para a coluna de id de uma tabela dos Serviços Móveis. Por exemplo, se você quiser identificar cada registro por um endereço de email, poderá usar o objeto JSON a seguir.

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified 
		// data compared to what was passed to the server.
	}];	

Se um valor de id de cadeia de caracteres não for fornecido quando novos registros forem inseridos em uma tabela, os Serviços Móveis gerarão um valor exclusivo para a id.

O suporte às ids de cadeia de caracteres oferece as seguintes vantagens aos desenvolvedores

+ As ids podem ser geradas sem necessidade de uma viagem de ida e volta ao banco de dados.
+ Os registros são mais fáceis de mesclar de diferentes tabelas ou bancos de dados.
+ Os valores das ids podem se integrar melhor à lógica de um aplicativo.

Você também pode usar scripts de servidor para definir valores de id. O exemplo de script a seguir gera um GUID personalizado e o atribui à id de um novo registro. Ele é semelhante ao valor de id que os Serviços Móveis gerariam se você não passasse um valor para uma id de registro.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Se um aplicativo fornecer um valor para uma id, os Serviços Móveis o armazenarão como está. Isso inclui espaços em branco à direita ou à esquerda. O espaço em branco não será cortado do valor.

O valor da `id` deve ser exclusivo e não deve incluir caracteres dos seguintes conjuntos:

+ Caracteres de controle: [0x0000-0x001F] e [0x007F-0x009F]. Para obter mais informações, consulte [Códigos de controle ASCII C0 e C1].
+  Caracteres imprimíveis: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  As ids "." e ".."

Como alternativa, você pode usar IDs de inteiros para as suas tabelas. Para usar uma ID de inteiro, você deve criar sua tabela com o comando `mobile table create`, usando a opção `--integerId`. Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas dos Serviços Móveis].

Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base nos campos do objeto na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico].

<h2><a name="modifying"></a><span class="short-header">Modificando dados</span>Como modificar dados em um serviço móvel</h2>

Atualize um objeto existente, modificando um item retornado de uma consulta anterior e, em seguida, chamando a função **update**.

	NSMutableDictionary *item = [self.results.item objectAtIndex:0];
	[item setObject:@YES forKey:@"complete"];
	[table update:item completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Quando fizer atualizações, você só precisará fornecer o campo que está sendo atualizado, junto com a ID de linha, como no exemplo a seguir:

	[table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @Yes} completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];
	
	
Para excluir um item da tabela, simplesmente passe o item para o método de exclusão, da seguinte maneira:

	[table delete:item completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

You can also just delete a record using its id directly, as in the following example:

	[table deleteWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];	

Observe que, no mínimo, o atributo `id` deverá ser definido quando você fizer atualizações e exclusões.

<h2><a name="authentication"></a><span class="short-header">Autenticação</span>Como autenticar usuários</h2>

Os Serviços Móveis permitem que você use os provedores de identidade a seguir para autenticar os usuários:

- Facebook
- Google 
- Conta da Microsoft
- Twitter
- Azure Active Directory

Para obter mais informações sobre como configurar um provedor de identidade, consulte [Introdução à autenticação]. 

Os Serviços Móveis oferecem suporte aos dois fluxos de trabalho de autenticação as seguir:

- Em um logon gerenciado pelo servidor, os Serviços Móveis gerenciam o processo de logon em nome do seu aplicativo. Uma página de logon específica do provedor é exibida pela a biblioteca de cliente, e os Serviços Móveis fazem o trabalho de autenticação com o provedor escolhido.

- Em um logon gerenciado pelo cliente, o aplicativo deve solicitar um token do provedor de identidade e, em seguida, apresentá-lo aos Serviços Móveis para autenticação.

Quando a autenticação for bem-sucedida, um objeto de usuário será retornado contendo o valor de ID de usuário atribuído e o token de autenticação. Você pode usar essa ID de usuário nos scripts de servidor para validar ou modificar solicitações. Para obter mais informações, consulte [Usar scripts para autorizar usuários]. O próprio token pode ser armazenado em cache com segurança para ser usado nos logons subsequentes. 

Você também pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Para obter mais informações, consulte [Permissões].

### Logon gerenciado pelo servidor

Este é um exemplo de como fazer logon usando uma Conta da Microsoft. Esse código pode ser chamado no ViewDidLoad do seu controlador ou disparado manualmente de um UIButton. Isso exibirá uma IU padrão para fazer logon no provedor de identidade.

	[client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
		completion:^(MSUser *user, NSError *error) {
			NSString *msg;
			if(error) {
				msg = [@"An error occured: " stringByAppendingString:error.description];
			} else {
				msg = [@"You are logged in as " stringByAppendingString:user.userId];
			}
		
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
								  message:msg 
								  delegate:nil 
								  cancelButtonTitle:@"OK" 
								  otherButtonTitles: nil];
			[alert show];
	}];	

Observação: se você estiver usando um provedor de identidade diferente do da Conta da Microsoft, altere o valor passado para o método de logon acima para um destes: `facebook`, `twitter`, `google` ou `windowsazureactivedirectory`.

Você também pode obter uma referência ao MSLoginController e exibi-la usando:

	-(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Logon gerenciado pelo (logon único)

Há casos em que o processo de logon é feito fora do cliente dos Serviços Móveis. Você pode fazer isso para habilitar uma funcionalidade de logon único ou quando seu aplicativo precisar entrar em contato diretamente com o provedor de identidade para obter informações sobre o usuário. Nesses casos, você poderá fazer logon nos Serviços Móveis, fornecendo um token obtido independentemente de um provedor de identidade com suporte.

O exemplo a seguir usa o [SDK do Live Connect] para habilitar o logon único para aplicativos iOS. 
	
	[client loginWithProvider:@"microsoftaccount" 
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
			self.loggedInUser = user;
			NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
				message:msg 
				delegate:nil 
				cancelButtonTitle:@"OK" 
				otherButtonTitles: nil];
			[alert show];
	}];

Esse código pressupõe que você tenha criado anteriormente uma instância **LiveConnectClient** denominada `liveClient` no controlador e o usuário esteja conectado.

###<a name="caching-tokens"></a>Como: armazenar tokens de autenticação em cache

Para impedir que os usuários tenham que se autenticar sempre que executarem seu aplicativo, você pode armazenar a identidade atual do usuário em cache depois que ele fizer logon. Depois disso, você poderá usar essas informações para criar o usuário diretamente e ignorar o processo de logon.  Para isso, armazene a ID do usuário e o token de autenticação localmente. No exemplo a seguir, o token é armazenado em cache com segurança no [KeyChain].

	- (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
	{
		NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
		[query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
		[query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
    	
		if(isSearch) {
			// Use the proper search constants, return only the attributes of the first match.
			[query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
		}
    
		return query;
	}

	- (IBAction)loginUser:(id)sender {
		NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
		CFDictionaryRef cfresult;

		OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
		if (status == noErr) {
			NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;
			
			//create an MSUser object
			MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
			NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
			user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
			[self.todoService.client setCurrentUser:user];
			
		} else if (status == errSecItemNotFound) {
			//we need to log the user in
			[self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
				completion:^(MSUser *user, NSError *error) {
					NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
											message:msg delegate:nil 
											cancelButtonTitle:@"OK" 
											otherButtonTitles: nil];
					[alert show];
                
					//save the user id and token to the KeyChain
					NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
															andIsSearch:NO];
					[newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
					[newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
					OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
					if(status != errSecSuccess) {
						//handle error as needed
						NSAssert(NO, @"Error caching password.");
					}
			}];
		}	

<div class="dev-callout"><strong>Observação</strong>
<p>Os tokens são dados confidenciais e devem ser armazenados criptografados caso o dispositivo seja perdido ou roubado.</p>
</div>

Ao usar um token armazenado em cache, um usuário não precisará fazer logon novamente até o token expirar. Quando um usuário tentar fazer logon com um token expirado, a resposta 401 não autorizado será retornada. Nesse ponto, o usuário deverá fazer logon novamente para obter um novo token, que poderá ser armazenado em cache outra vez. Você pode usar filtros para não precisar escrever um código que trate tokens expirados sempre que o seu aplicativo chamar o serviço móvel.  OS filtros permitem a interceptação das chamadas do seu serviço móvel e das respostas do seu serviço. O código no filtro testa a resposta para uma 401, dispara o processo de logon se o token tiver expirado e, em seguida, tenta novamente a solicitação que gerou a 401. Para obter detalhes, consulte [Tratando tokens expirados].

<h2><a name="errors"></a><span class="short-header">Tratamento de erros</span>Como tratar erros</h2>

Quando é feita uma chamada para o serviço móvel, o bloco de conclusão contém um parâmetro `NSError *error`. Quando ocorre um erro, esse parâmetro será retornado como um valor não nulo. No seu código, você deve marcar esse parâmetro e tratar o erro conforme necessário.

Quando ocorre um erro, você pode obter mais informações, incluindo o arquivo MSError.h no código. Esse arquivo define as constantes a seguir que você pode usar para acessar os dados adicionais de `[error userInfo]`:

+ **MSErrorResponseKey**: os dados de resposta HTTP associados ao erro
* **MSErrorRequestKey**: os dados de solicitação HTTP associados ao erro

Além disso, uma constante é definida para cada código de erro. Uma explicação desses códigos pode ser encontrada no arquivo MSError.h.

Para obter um exemplo de como executar a validação e tratar de qualquer validação, consulte [Validar e modificar dados nos Serviços Móveis usando scripts de servidor]. Neste tópico, a validação do lado do servidor é implementada por meio de scripts de servidor. Quando dados inválidos são enviados, uma resposta de erro é retornada e é tratada pelo cliente.

<!--
<h2><a name="#unit-testing"></a><span class="short-header">Criando testes</span>Como: criar testes de unidade</h2>

_(Opcional) Esta seção mostra como escrever um teste de unidade ao usar a biblioteca de cliente (informações de Yavor)._

<h2><a name="#customizing"></a><span class="short-header">Personalizando o cliente</span>Como: personalizar o cliente</h2>

_(Opcional) Esta seção mostra como enviar comportamentos do cliente personalizados._

###<a name="custom-headers"></a>Como: personalizar cabeçalhos de solicitação

_(Opcional) Esta seção mostra como enviar cabeçalhos de solicitação personalizados._

Para obter mais informações, consulte o novo tópico sobre processamento de cabeçalhos no lado do servidor.

###<a name="custom-serialization"></a>Como: personalizar a serialização

_(Opcional) Esta seção mostra como usar atributos para personalizar o modo como os tipos de dados são serializados._

Para obter mais informações, consulte o novo tópico sobre processamento de cabeçalhos no lado do servidor.

## <a name="next-steps"></a>Próximas etapas
-->

<!-- Anchors. -->

[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Configuração e pré-requisitos]: #Setup
[Como: Criar o cliente dos Serviços Móveis]: #create-client
[Como: criar uma referência de tabela]: #table-reference
[Como: consultar dados de um serviço móvel]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados nas páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Como: associar dados à interface do usuário]: #binding
[Como: inserir dados em um serviço móvel]: #inserting
[Como modificar dados em um serviço móvel]: #modifying
[Como: autenticar usuários]: #authentication
[Armazenar tokens de autenticação em cache]: #caching-tokens
[Como: carregar imagens e arquivos grandes]: #blobs
[Como: tratar erros]: #errors
[How to: Design unit tests]: #unit-testing 
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: /pt-br/develop/mobile/tutorials/get-started-ios
[Validar e modificar dados nos Serviços Móveis usando scripts de servidor]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[SDK do iOS]: https://developer.apple.com/xcode

[Tratando tokens expirados]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[SDK do Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissões]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193161.aspx
[Usar scripts para autorizar usuários]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinâmico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Como: acessar parâmetros personalizados]: /pt-br/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Criar uma tabela]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193162.aspx
[Objeto NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI para gerenciar tabelas dos Serviços Móveis]: http://www.windowsazure.com/pt-br/manage/linux/other-resources/command-line-tools/#Mobile_Tables

