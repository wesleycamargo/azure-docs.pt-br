<properties linkid="mobile-services-how-to-html-client" urlDisplayName="Cliente HTML" pageTitle="Como usar um cliente HTML - Serviços Móveis do Azure" metaKeywords="Serviços Móveis do Azure, Cliente HTML dos Serviços Móveis, cliente HTML" description="Saiba como usar um cliente HTML para os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure" authors=""  solutions="" writer="krisragh" manager="" editor=""  />


# Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/pt-br/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/pt-br/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Este guia mostra como executar cenários comuns usando um cliente HTML/JavaScript para os Serviços Móveis do Azure. Os cenários abrangidos incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se você for novo nos Serviços Móveis, deverá considerar primeiro concluir os Serviços Móveis [Início rápido do JavaScript da Windows Store] ou [Início rápido do HTML]. O tutorial de início rápido ajuda a configurar sua conta e criar seu primeiro serviço móvel.


## Sumário

- [O que são Serviços Móveis]
- [Conceitos]
- [Como criar o cliente dos Serviços Móveis]
- [Como consultar dados de um serviço móvel]
	- [Filtrar dados retornados]
    - [Classificar dados retornados]
	- [Retornar dados em páginas]
	- [Selecionar colunas específicas]
	- [Pesquisar dados por ID]
- [Como inserir dados em um serviço móvel]
- [Como modificar dados em um serviço móvel]
- [Como excluir dados em um serviço móvel]
- [Como exibir dados na interface do usuário]
- [Como autenticar usuários]
- [Como tratar erros]
- [Como usar promessas]
- [Como personalizar cabeçalhos de solicitação]
- [Como usar o compartilhamento de recursos entre origens]
- [Próximas etapas]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="create-client"></a><span class="short-header">Criar o cliente dos Serviços Móveis</span>Como criar o cliente dos Serviços Móveis</h2>

O código a seguir cria uma instância do objeto do cliente de serviço móvel. 

Em seu editor da web, abra o arquivo HTML e adicione o seguinte para as referências de script da página:

	        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

No editor, abra ou crie um arquivo JavaScript e adicione o seguinte código que define a variável `MobileServiceClient` e fornece a URL e a chave do aplicativo do serviço móvel no construtor `MobileServiceClient`, nessa ordem. Você deve substituir o espaço reservado `AppUrl` pela URL do aplicativo do seu serviço móvel e `AppKey` pela chave do aplicativo. Para saber como obter a URL e a chave do aplicativo para o serviço móvel, consulte o tutorial [Introdução a dados em JavaScript na Windows Store] ou [Introdução a dados em HTML/JavaScript].

			var MobileServiceClient = WindowsAzure.MobileServiceClient;
		    var client = new MobileServiceClient('AppUrl', 'AppKey');

<h2><a name="querying"></a><span class="short-header">Consultando dados</span>Como consultar dados de um serviço móvel</h2>

Todo o código que acessa ou modifica os dados na tabela do Banco de Dados SQL chama funções no objeto `MobileServiceTable`. Você obtém uma referência à tabela chamando a função `getTable()` em uma instância do `MobileServiceClient`.
	
		    var todoItemTable = client.getTable('todoitem');



### <a name="filtering"></a>Como filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula `where` em uma consulta. Ele retorna todos os itens de `todoItemTable`cujo campo total é igual a `false`. `todoItemTable` é a referência à tabela de serviço móvel que criamos anteriormente. A função where aplica um predicado de filtragem de linha à consulta na tabela. Ela aceita como argumento um objeto JSON ou uma função que define o filtro de linha e retorna uma consulta que pode ser composta adicionalmente. 
	
			var query = todoItemTable.where({
			    complete: false
			}).read().done(function (results) {
			    alert(JSON.stringify(results));
			}, function (err) {
			    alert("Error: " + err);
			});

Adicionando a chamada `where` no objeto Query e passando um objeto como um parâmetro, estamos instruindo os Serviços Móveis a retornar apenas as linhas cuja coluna `complete` contém o valor `false`. Além disso, examine o URI da solicitação abaixo e observe que estamos modificando a própria cadeia de caracteres da consulta:

			GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1			

Você pode exibir o URI da solicitação enviado ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o Fiddler.

Essa solicitação normalmente é traduzida aproximadamente para a seguinte consulta SQL no lado do servidor:
			
			SELECT * 
			FROM TodoItem 			
			WHERE ISNULL(complete, 0) = 0

O objeto que é passado para o método `where` pode ter um número arbitrário de parâmetros, e eles serão todos interpretados como cláusulas AND para a consulta. Por exemplo, a linha abaixo:

			query.where({
			   complete: false,
			   assignee: "david",
			   difficulty: "medium"
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

É traduzida aproximadamente (para a mesma solicitação mostrada anteriormente) como
			
			SELECT * 
			FROM TodoItem 
			WHERE ISNULL(complete, 0) = 0
			      AND assignee = 'david'
			      AND difficulty = 'medium'

O demonstrativo `where` acima e a consulta SQL acima encontraram itens incompletos atribuídos a "david" de dificuldade "média".

No entanto, há outra maneira de escrever a mesma consulta. Uma chamada `where` no objeto de consulta adicionará uma expressão `AND` a cláusula `WHERE`, portanto, poderíamos ter escrito isso nas três linhas em vez disso:

			query.where({
			   complete: false
			});
			query.where({
			   assignee: "david"
			});
			query.where({
			   difficulty: "medium"
			});

Ou usar a API fluente:

			query.where({
			   complete: false
			})
			   .where({
			   assignee: "david"
			})
			   .where({
			   difficulty: "medium"
			});

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável. Todas as chamadas `where` até agora introduzem um objeto com alguns parâmetros e são comparadas de igualdade em relação aos dados do banco de dados. No entanto, há outra sobrecarga para o método de consulta, que assume uma função em vez do objeto. Nesta função podemos então escrever expressões mais complexas, usando operadores tais como desigualdade e outras operações relacionais. Nessas funções, a palavra-chave `this` vincula-se ao objeto do servidor.

O corpo da função é traduzido em uma expressão OData boolean que é passada para um parâmetro de cadeia de caracteres de consulta. É possível passar em uma função que não recebe parâmetros, da seguinte forma:

		    query.where(function () {
		       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
		    }).read().done(function (results) {
		       alert(JSON.stringify(results));
		    }, function (err) {
		       alert("Error: " + err);
		    });


Se passando em uma função com parâmetros, quaisquer argumentos após a cláusula `where` são associados aos parâmetros da função na ordem. Quaisquer objetos que vêm de fora do escopo da função DEVEM ser passados como parâmetros - a função não pode capturar quaisquer variáveis externas. Nos próximos dois exemplos, o argumento "david" está associado ao parâmetro `nome` e no primeiro exemplo, o argumento "medium" também está associado ao parâmetro `nível`. Além disso, a função deve consistir em uma única demonstração `return` com uma expressão suportada, da seguinte forma:
					
			 query.where(function (name, level) {
			    return this.assignee == name && this.difficulty == level;
			 }, "david", "medium").read().done(function (results) {
			    alert(JSON.stringify(results));
			 }, function (err) {
			    alert("Error: " + err);
			 });

Então, desde que seguirmos as regras, podemos adicionar filtros mais complexos para nossas consultas de banco de dados, da seguinte forma:

		    query.where(function (name) {
		       return this.assignee == name &&
		          (this.difficulty == "medium" || this.difficulty == "low");
		    }, "david").read().done(function (results) {
		       alert(JSON.stringify(results));
		    }, function (err) {
		       alert("Error: " + err);
		    });

É possível combinar `where` com o `orderBy`, `Executar` e `Ignorar`. Consulte a próxima seção para obter detalhes.

### <a name="sorting"></a>Como classificar dados retornados

O código a seguir ilustra como classificar dados incluindo uma função `orderBy` ou `orderByDescending` na consulta. Ele retorna os itens da `todoItemTable` classificada em ordem crescente pelo campo `texto`. Por padrão, o servidor retorna apenas os 50 primeiros elementos. 

<div class="dev-callout"><strong>Nota</strong> <p>Um tamanho da página de servidor controlado por nós usado por padrão para prevenir todos os elementos de serem retornados Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço. </p> </div>
>
Você pode aumentar o número de itens a ser retornado pela chamada `take` conforme descrito na próxima seção. `todoItemTable` é a referência à tabela de serviço móvel que criamos anteriormente.

			var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

			var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

			var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

### <a name="paging"></a>Como retornar dados em páginas

O código a seguir mostra como implementar a paginação de dados retornados usando as cláusulas `skip` e `take` na consulta.  A consulta a seguir, quando executada, retorna os três itens principais na tabela. 

			var query = todoItemTable.take(3).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Observe que o método `take(3)` foi traduzido para a opção de consulta `$top=3` na consulta URI.

A seguinte consulta revisada ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

			var query = todoItemTable.skip(3).take(3).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Novamente, você pode ver a URI da solicitação enviada ao serviço móvel. Observe que o método `skip(3)`foi convertido para a opção de consulta `$skip=3` no URI da consulta.

Esse é um cenário simplificado de passar valores de paginação embutidos em código para as funções `take` e `skip`. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. 

### <a name="selecting"></a>Como selecionar colunas específicas

Você pode especificar qual conjunto de propriedades incluir nos resultados adicionando uma cláusula `select` à sua consulta. Por exemplo, o código a seguir retorna as propriedades `id`, `complete` e `text` de cada linha na `todoItemTable`:

			var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			})
	
Aqui os parâmetros para a função select são os nomes das colunas da tabela que você deseja retornar. 


Todas as funções descritas até agora são aditivas, portanto, podemos continuar a chamá-las e a afetar a consulta a cada vez. Mais um exemplo:


		    query.where({
		       complete: false
		    })
		       .select('id', 'assignee')
		       .orderBy('assignee')
		       .take(10)
		       .read().done(function (results) {
		       alert(JSON.stringify(results));
		    }, function (err) {
		       alert("Error: " + err);

### <a name="lookingup"></a>Como pesquisar dados pela ID

A função `lookup` usa apenas o valor de `id` e retorna o objeto do banco de dados com essa ID. Tabelas de banco de dados são criadas com uma coluna de números inteiros ou de `id` de cadeia de caracteres. Uma coluna de `id` de cadeia de caracteres é o padrão.

			todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			})



<h2><a name="inserting"></a><span class="short-header">Inserindo dados</span>Como inserir dados em um serviço móvel</h2>

O código a seguir ilustra como inserir novas linhas em uma tabela. O cliente solicita que uma linha de dados seja inserida enviando uma solicitação POST ao serviço móvel. O corpo da solicitação contém os dados a serem inseridos, como um objeto JSON. 

			todoItemTable.insert({
			   text: "New Item",
			   complete: false
			})

Isso insere dados do objeto JSON fornecido na tabela. Você também pode especificar uma função de retorno de chamada a ser invocada quando a inserção for concluída:

			todoItemTable.insert({
			   text: "New Item",
			   complete: false
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});


Os Serviços Móveis oferecem suporte a valores exclusivos e personalizados de cadeia de caracteres para a id da tabela. Isso permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário, para a coluna de id de uma tabela dos Serviços Móveis. Por exemplo, se você quiser identificar cada registro por um endereço de email, poderá usar o seguinte objeto JSON.

			todoItemTable.insert({
			   id: "myemail@domain.com",				
			   text: "New Item",
			   complete: false
			})

Se um valor de id de cadeia de caracteres não for fornecido ao inserir novos registros em uma tabela, os Serviços Móveis gerarão um valor exclusivo para a id.

O suporte às ids de cadeia de caracteres oferece as seguintes vantagens aos desenvolvedores

+ As ids podem ser geradas sem necessidade de uma viagem de ida e volta ao banco de dados.
+ Os registros são mais fáceis de mesclar em diferentes tabelas ou bancos de dados.
+ Os valores das ids podem se integrar melhor com a lógica de um aplicativo.

Você também pode usar scripts de servidor para definir valores de ids. O exemplo de script a seguir gera um GUID personalizado e o atribui a uma nova id de registro. Esse é semelhante ao valor da id que os Serviços Móveis gerariam se você não passasse um valor para uma id de registro.

	//Exemplo de geração de uma id. Isso não será necessário uma vez que os Serviços Móveis
	//gerarão uma id se uma não for passada.
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
+  Caracteres imprimíveis: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  As ids "." e ".."

Como alternativa, você pode usar Ids de números inteiros para suas tabelas. Para usar uma Id de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId`. Esse comando é usado com a CLI (Interface de Linha de Comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas de Serviços Móveis].


<h2><a name="modifying"></a><span class="short-header">Modificando dados</span>Como modificar dados em um serviço móvel</h2>

O código a seguir ilustra como atualizar dados em uma tabela. O cliente solicita que uma linha de dados seja atualizada enviando uma solicitação PATCH ao serviço móvel. O corpo da solicitação contém os campos específicos a serem atualizados, como um objeto JSON. Ele atualiza um item existente na tabela `todoItemTable`. 

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

O primeiro parâmetro especifica a instância a ser atualizada na tabela, conforme especificado pela sua ID. 

Você também pode especificar uma função de retorno de chamada a ser invocada quando a atualização for concluída:

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});
	
<h2><a name="deleting"></a><span class="short-header">Excluindo dados</span>Como excluir dados em um serviço móvel</h2>

O código a seguir ilustra como excluir dados de uma tabela. O cliente solicita que uma linha de dados seja excluída enviando uma solicitação DELETE ao serviço móvel. Ele exclui um item existente na tabela todoItemTable. 

			todoItemTable.del({
			   id: idToDelete
			})

O primeiro parâmetro especifica a instância a ser excluída na tabela, conforme especificado pela sua ID. 

Você também pode especificar uma função de retorno de chamada a ser invocada quando a exclusão for concluída:	
	
			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});	

<h2><a name="binding"></a><span class="short-header">Exibindo dados</span>Como exibir dados na interface do usuário</h2>

Esta seção mostra como exibir os objetos de dados retornados usando elementos da interface do usuário. Para consultar itens na `todoItemTable` e exibi-los em uma lista muito simples, você pode executar o código de exemplo a seguir. Nenhuma seleção, filtragem ou classificação de qualquer tipo é feita. 

			var query = todoItemTable;
		
			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoItems.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfItems.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Em um aplicativo da Windows Store, os resultados de uma consulta podem ser usados para criar um objeto [WinJS.Binding.List], que pode ser vinculado como a fonte de dados para um objeto [ListView]. Para obter mais informações, consulte [Associação de dados (aplicativos da Windows Store usando JavaScript e HTML)].

<h2><a name="caching"></a><span class="short-header">Autenticação</span>Como autenticar usuários</h2>

Os Serviços Móveis oferecem suporte a autenticação e a autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: o Facebook, o Google, a Conta da Microsoft e o Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

Dois fluxos de autenticação são suportados: um _server flow_ e um _client flow_. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

<h3>Fluxo de servidor</h3>
Para que os Serviços Móveis gerenciem o processo de autenticação em seu aplicativo da Windows Store ou HTML5, você deve registrar seu aplicativo no provedor de identidade. Em seguida, no seu serviço móvel, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial "Introdução à autenticação" ([Windows Store][Introdução à autenticação da Windows Store]/[HTML][Introdução à autenticação]).

Depois de registrar seu provedor de identidade, basta chamar o [método LoginAsync] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, para fazer logon com o Facebook, use o código a seguir. 

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Se você estiver usando um provedor de identidade além do Facebook, altere o valor passado para o método `login` acima para um dos seguintes: `microsoftaccount`,`facebook`, `twitter`, `google`, ou `windowsazureactivedirectory`.

Nesse caso, os Serviços Móveis gerenciam o fluxo de autenticação OAuth 2.0 exibindo a página de logon do provedor selecionado e gerando um token de autenticação dos Serviços Móveis depois de um logon bem-sucedido com o provedor de identidade. A função [login], quando concluída, retorna um objeto JSON (**user**) que expõe o ID do usuário e o token de autenticação dos Serviços Móveis nos campos **userId** e **authenticationToken**, respectivamente. Esse token pode ser armazenado em cache e reutilizado até que expire. Para obter mais informações, consulte [Armazenando o token de autenticação em cache].

<div class="dev-callout"><b>Aplicativo da Windows Store</b>
<p>Ao usar o provedor de logon da Conta da Microsoft para autenticar os usuários de seu aplicativo da Windows Store, você também deve registrar o pacote de aplicativos nos Serviços Móveis. Ao registrar as informações do pacote de aplicativos do Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experiência de logon única. Se você não fizer isso, os usuários de login da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Para saber como registrar seu pacote de aplicativos da Windows Store, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft.</a> Após as informações do pacote serem registradas nos Serviços Móveis, chame o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> fornecendo um valor de <strong>true</strong> para o parâmetro <em>useSingleSignOn</em> para reutilizar as credenciais.</p>
</div>

<h3>Fluxo de cliente</h3>
Seu aplicativo também pode entrar em contato independentemente com o provedor de identidade e fornecer o token retornado aos Serviços Móveis para autenticação. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade. 

O exemplo a seguir usa o Live SDK, que oferece suporte a logon único para aplicativos da Windows Store, usando a Conta da Microsoft:

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount", 
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

Esse exemplo simplificado obtém um token do Live Connect, que é fornecido aos Serviços Móveis chamando a função [login]. Para obter um exemplo mais completo de como usar a Conta da Microsoft para fornecer uma experiência de logon único, consulte [Autenticar seu aplicativo com logon único].

Ao usar as APIs do Facebook ou do Google para autenticação do cliente, o exemplo muda levemente. 

		client.login(
		     "facebook", 
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Esse exemplo pressupõe que o token fornecido pelo respectivo SDK do provedor é armazenado na variável `token`.
O Twitter não pode ser usado para autenticação de cliente desta vez. 

<h3>Armazenando o token de autenticação em cache</h3>
Em alguns casos, a chamada para o método login pode ser evitada após a primeira vez que o usuário é autenticado. Podemos usar [sessionStorage] ou [localStorage] para armazenar a identidade do usuário atual na primeira vez que ele fizer logon e em cada vez subsequente que verificarmos se já temos a identidade do usuário em nosso cache. Se o cache estiver vazio ou a chamada falhar (ou seja, a sessão de logon atual tiver expirado), ainda precisaremos passar pelo processo de logon. 

        // Após fazer o logon
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Fazer Logon 
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Fluxo de logon regular
       }

         // Fazer logoff
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a><span class="short-header">Tratamento de erros</span>Como tratar erros</h2>

Há várias maneiras de encontrar, validar e resolver erros nos Serviços Móveis. 

Por exemplo, os scripts de servidor são registrados em um serviço móvel e podem ser usados para executar um grande intervalo de operações nos dados que estão sendo inseridos e atualizados, incluindo validação e modificação de dados. Imagine definir e registrar um script de servidor que valide e modifique dados, da seguinte forma:

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

Esse script do lado do servidor valida o comprimento dos dados da cadeia de caracteres enviados ao serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

Agora que o serviço móvel está validando dados e enviando respostas de erros no lado do servidor, você pode atualizar seu aplicativo HTML para poder tratar de respostas de erros de validação.

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


Para evidenciar isso ainda mais, você passa o manipulador de erro como o segundo argumento cada vez que executar o acesso aos dados: 
			
			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a><span class="short-header">Promessas</span>Como usar promessas</h2>

As promessas fornecem um mecanismo para agendar o trabalho a ser feito em um valor que ainda não foi computado. São uma abstração para o gerenciamento de interações com APIs assíncronas. 

A promessa `done` é executada assim que a função fornecida a ela foi concluída com êxito ou obteve um erro. Ao contrário da promessa `then`, ela gera qualquer erro que não seja tratado dentro da função e depois que os manipuladores concluírem a execução, essa função gera qualquer erro que tenha sido retornado, como uma promessa no estado errado. Para obter mais informações, consulte [done].

			promise.done(onComplete, onError);

Desta forma:
	
			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

A promessa `then` é igual a promessa `done`, mas ao contrário da promessa `then`, `done` gera qualquer erro que não é tratado dentro da função. Se você não fornecer um manipulador de erro para `then`, e a operação tiver um erro, ela não gerará uma exceção, mas, em vez disso, retornará uma promessa no estado de erro. Para obter mais informações, consulte [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Desta forma:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Você pode usar promessas de várias maneiras diferentes. Você pode encadear operações de promessas chamando `then` ou `done` na promessa que é retornada pela função anterior `then`. Use `then` para um estágio intermediário da operação (por exemplo `.then().then()`) e `done` para o estágio final da operação (por exemplo `.then().then().done()`).  Você pode encadear várias funções `then`, porque `then` retorna uma promessa. Não é possível encadear mais de um método `done`, porque ele retorna indefinido. [Saiba mais sobre as diferenças entre then e done]
	
 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a><span class="short-header">Personalizar cabeçalhos de solicitações</span>Como personalizar cabeçalhos de solicitações de clientes</h2>

Você pode enviar cabeçalhos de solicitações personalizados usando a função `withFilter`, ler e escrever propriedades arbitrárias da solicitação prestes a ser enviada com o filtro. Talvez você queira adicionar um cabeçalho personalizado HTTP desse tipo se um script do lado do servidor precisar ou puder ser melhorado por ele. 

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

Os filtros são usados para muito mais do que cabeçalhos de solicitações personalizados. Eles podem ser usados para examinar ou alterar solicitações, examinar ou alterar respostas, ignorar chamadas de rede, enviar várias chamadas etc.

<h2><a name="hostnames"></a><span class="short-header">Usar o CORS</span>Como usar o compartilhamento de recursos entre origens</h2>

Para controlar quais sites podem interagir e enviar solicitações para o seu serviço móvel, certifique-se de adicionar o nome do host do site que você usa para hospedá-lo à lista branca do CORS (Compartilhamento de Recursos entre Origens) usando a guia Configuração. Você pode usar caracteres curinga se necessário. Por padrão, novos Serviços Móveis instruem os navegadores para permitirem acesso somente a partir do `localhost`, e o CORS (Compartilhamento de Recursos entre Origens) permite que o código em JavaScript em execução em um navegador em um nome de host externo interaja com o Serviço Móvel.  Essa configuração não é necessária para aplicativos WinJS.

<h2><a name="nextsteps"></a>Próximas etapas</h2>

Agora que você concluiu as instruções deste tópico de referência conceitual, saiba como executar tarefas importantes nos Serviços Móveis em detalhes:

* [Começar com os Serviços Móveis]
  <br/>Aprenda os conceitos básicos de como usar os Serviços Móveis.

* [Começar com os dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários do seu aplicativo com um provedor de identidade.

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Aprenda a usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

* [Autorizar usuários com scripts]
  <br/>Saiba como usar o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usá-lo para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[O que são Serviços Móveis]: #what-is
[Conceitos]: #concepts
[Como criar o cliente dos Serviços Móveis]: #create-client
[Como consultar dados de um Serviço Móvel]: #querying
[Filtrar dados retornados]: #filtering
[Classificar dados retornados]: #sorting
[Retornar dados em páginas]: #paging
[Selecionar colunas específicas]: #selecting
[Pesquisar dados por ID]: #lookingup
[Como exibir dados na interface do usuário]: #binding
[Como inserir dados em um serviço móvel]: #inserting
[Como modificar dados em um serviço móvel]: #modifying
[Como excluir dados em um serviço móvel]: #deleting
[Como autenticar usuários]: #caching
[Como tratar erros]: #errors
[Como usar promessas]: #promises
[Como personalizar cabeçalhos de solicitação]: #customizing
[Como usar o compartilhamento de recursos entre origens]: #hostnames
[Próximas etapas]: #nextsteps



<!-- URLs. -->
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-html
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Introdução a dados]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-html/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html
[Introdução à autenticação da Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/pt-br/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh701079.aspx
[Saiba mais sobre as diferenças entre then e done]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh700334.aspx
[Como tratar erros em promessas]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/pt-br/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/pt-br/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/pt-br/library/windows/apps/br211837.aspx
[Vinculação de dados (aplicativos da Windows Store usando JavaScript e HTML)]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh758311.aspx
[Início rápido de JavaScript da Windows Store]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started
[Início rápido de HTML]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-html
[Introdução a dados com JavaScript da Windows Store]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-js
[Introdução a dados em HTML/JavaScript]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-html/
[Você pode ver um exemplo completo de como configurar este cenário aqui]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-html
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-html
[logon]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554236.aspx
[Autenticar seu aplicativo com logon único]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[Códigos de controle ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI para gerenciar tabelas dos Serviços Móveis]: http://www.windowsazure.com/pt-br/manage/linux/other-resources/command-line-tools/#Mobile_Tables

