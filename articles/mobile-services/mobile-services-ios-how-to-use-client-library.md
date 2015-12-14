<properties
	pageTitle="Aprenda a usar a Biblioteca de Cliente iOS para os Serviços Móveis do Azure."
	description="Como usar a Biblioteca de Cliente iOS para os Serviços Móveis"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Aprenda a usar a Biblioteca de Cliente iOS para os Serviços Móveis do Azure.

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando os Serviços Móveis do Azure [SDK do iOS]. Se você for novo nos Serviços Móveis, primeiro conclua o [Início rápido dos serviços móveis] para configurar sua conta, crie uma tabela e um serviço móvel.

> [AZURE.NOTE]Este guia usa a versão mais recente do [SDK de Serviços Móveis do iOS](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Se o seu projeto usa uma versão mais antiga do SDK, primeiro atualize a estrutura no Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Configuração e pré-requisitos

Este guia pressupõe que você tenha criado um serviço móvel com uma tabela. Para saber mais, consulte a seção [Criar uma tabela] ou reutilize a tabela `TodoItem` criada em [Início rápido dos serviços móveis]. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais. Este guia também pressupõe que o Xcode referencia `WindowsAzureMobileServices.framework` e importa `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Como criar o cliente Serviços Móveis

Para acessar um serviço móvel do Azure em seu projeto, crie um objeto de cliente `MSClient`. Substitua `AppUrl` e `AppKey` com a URL do serviço móvel e com os valores de painel da chave de aplicativo, respectivamente.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Como criar uma referência de tabela

Para acessar ou atualizar dados no serviço móvel do Azure, crie uma referência à tabela. Substitua `TodoItem` pelo nome da sua tabela.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Como consultar dados

Para criar uma consulta de banco de dados, consulte o objeto `MSTable`. A consulta a seguir obtém todos os itens em `TodoItem` e registra o texto de cada item.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>Como filtrar dados retornados

Para filtrar os resultados, há muitas opções disponíveis.

Para filtrar usando um predicado, use `NSPredicate` e `readWithPredicate`. Os filtros a seguir retornaram dados para localizar apenas os itens pendentes não concluídos.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>Como usar o MSQuery

Para executar uma consulta complexa (incluindo classificação e paginação), crie um objeto de `MSQuery` diretamente ou usando um predicado:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

O `MSQuery` permite que você controle vários comportamentos de consulta, incluindo o que está a seguir. Execute uma consulta `MSQuery` chamando `readWithCompletion` nela, conforme mostrado no exemplo seguinte. * Especificar ordem de resultados * Limitar quais campos retornar * Limitar quantos registros retornar * Especificar contagem total na resposta * Especificar parâmetros de cadeia de caracteres de consulta personalizados na solicitação * Aplicar funções adicionais


## <a name="sorting"></a>Como classificar dados com MSQuery

Para classificar os resultados, vamos examinar um exemplo. Para primeiro classificar por ordem ascendente pelo campo `text` e descendente pelo campo `completion`, invoque `MSQuery` desta forma:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

## <a name="paging"></a>Como retornar dados em páginas com MSQuery

Os Serviços Móveis limitam a quantidade de registros retornados em uma única resposta. Para controlar o número de registros exibidos aos usuários, você deve implementar um sistema de paginação. A paginação é executada usando as três propriedades a seguir do objeto **MSQuery**:

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

No exemplo abaixo, uma função simples solicita 5 registros do servidor e acrescenta-os à coleção local dos registros carregados anteriormente:

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Como limitar campos e expandir os parâmetros de cadeia de caracteres de consulta com MSQuery

Para limitar os campos a serem retornados em uma consulta, especifique os nomes dos campos na propriedade **selectFields**. Isso retorna apenas o texto e os campos preenchidos:

```
	query.selectFields = @[@"text", @"completed"];
```

Para incluir parâmetros de cadeia de caracteres de consulta adicionais na solicitação do servidor (por exemplo, porque é usado por um script personalizado do lado do servidor), popule `query.parameters` desta forma:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Como inserir dados

Para inserir uma nova linha na tabela, crie um novo `NSDictionary` e invoque `table insert`. Os serviços móveis geram automaticamente novas colunas com base em `NSDictionary` quando a opção [Esquema dinâmico] não está desabilitada.

Se não for fornecida uma `id`, o back-end gera automaticamente uma ID exclusiva. Forneça sua própria `id` para usar endereços de email, nomes de usuários, ou seus próprios valores personalizados como ID. Fornecer sua própria identificação pode facilitar junções e lógicas de banco de dados comerciais.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Como modificar dados

Para atualizar uma linha existente, modifique um item e chame `update`:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como alternativa, forneça a ID da linha e o campo atualizado:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

No mínimo, o atributo `id` deve ser definido quando você faz atualizações.

##<a name="deleting"></a>Como excluir dados

Para excluir um item, invoque `delete` com o item:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Como alternativa, exclua fornecendo uma ID de linha:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

No mínimo, o atributo `id` deve ser definido quando você faz exclusões.

##<a name="#custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON. Para obter um exemplo de como criar uma API personalizada em seu serviço móvel, consulte [Como: definir um ponto de extremidade de API personalizado](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>Como autenticar usuários

Os Serviços Móveis do Azure dão suporte a vários provedores de identidade. Para um tutorial básico, confira [Autenticação].

Os Serviços Móveis do Azure dão suporte a dois fluxos de trabalho de autenticação :

- **Logon gerenciado por servidor**: os Serviços Móveis do Azure gerenciam o processo de logon em nome do seu aplicativo. Ele exibe uma página de logon específica do provedor e é autenticada com o provedor escolhido.

- **Logon gerenciado por cliente**: o _aplicativo_ solicita um token do provedor de identidade e o apresenta aos Serviços Móveis do Azure para autenticação.

Quando a autenticação é bem sucedida, você obtém um objeto de usuário com um valor de ID de usuário e o token de autenticação. Para usar essa ID de usuário e autorizar usuários, confira [Autorização do lado do serviço]. Para restringir o acesso à tabela somente a usuários autenticados, confira [Permissões].

### Logon gerenciado pelo servidor

Abaixo mostramos como se pode adicionar logon gerenciado pelo servidor ao projeto [Início Rápido dos Serviços Móveis]; você pode usar um código semelhante para os outros projetos. Para saber mais e ver um exemplo de ponta a ponta em ação, confira [Autenticação].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Logon gerenciado pelo cliente (logon único)

Você pode fazer o processo de logon fora do cliente Serviços Móveis, tanto para habilitar logon único quanto para o aplicativo contatar o provedor de identidade diretamente. Nesses casos, você pode fazer logon nos Serviços Móveis fornecendo um token obtido independentemente de um provedor de identidade com suporte.

O exemplo a seguir usa o [SDK do Live Connect] para habilitar o logon único para aplicativos iOS. Esse código pressupõe que você tenha uma instância **LiveConnectClient** denominada `liveClient` no controlador e que o usuário esteja conectado.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Como armazenar tokens de autenticação em cache

Vamos ver como você pode armazenar em cache tokens no projeto [Início Rápido dos Serviços Móveis]; você pode usar etapas semelhantes em qualquer projeto.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Como tratar erros

Quando uma chamada é feita ao serviço móvel, o bloco de conclusão contém um parâmetro `NSError *error`. Quando ocorre um erro, esse parâmetro é não nulo. No seu código, você deve marcar esse parâmetro e tratar o erro conforme necessário.

O arquivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define as constantes `MSErrorResponseKey`, `MSErrorRequestKey` e `MSErrorServerItemKey` para obter mais dados relacionados ao erro. Além disso, o arquivo define constantes para cada código de erro. Para obter um exemplo sobre como usar essas constantes, confira [Manipulador de conflito] para o uso de `MSErrorServerItemKey` e `MSErrorPreconditionFailed`.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Início rápido dos serviços móveis]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Autenticação]: /develop/mobile/tutorials/get-started-with-users-ios
[SDK do iOS]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[SDK do Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissões]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Autorização do lado do serviço]: mobile-services-javascript-backend-service-side-authorization.md
[Esquema dinâmico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Criar uma tabela]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Manipulador de conflito]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_1203_2015-->