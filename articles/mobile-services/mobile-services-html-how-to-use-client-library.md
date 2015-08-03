<properties 
	pageTitle="Como usar um cliente HTM - Serviços Móveis do Azure" 
	description="Saiba como usar um cliente HTML para serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>


# Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Visão geral

Este guia mostra como executar cenários comuns usando um cliente HTML/JavaScript para os Serviços Móveis do Azure, que inclui o JavaScript da Windows Store e aplicativos PhoneGap/Cordova. Os cenários abrangidos incluem consultas de dados, inserção, atualização e exclusão de dados, autenticação de usuários e tratamento de erros. Se for novo nos Serviços Móveis, você deve primeiro concluir o [início rápido dos Serviços Móveis](mobile-services-html-get-started.md). O tutorial de início rápido ajuda a configurar sua conta e criar seu primeiro serviço móvel.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Como criar o cliente Serviços Móveis

A maneira de adicionar uma referência ao cliente Serviços Móveis depende da sua plataforma de aplicativo, que inclui o seguinte:

- Para aplicativos baseados na Web, abra o arquivo HTML e adicione o seguinte às referências de script da página:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

- Para um aplicativo da Windows Store escrito em JavaScript/HTML, adicione o pacote NuGet **WindowsAzure.MobileServices.WinJS** ao seu projeto.

- Para um aplicativo PhoneGap ou Cordova, adicione o [plug-in de Serviços Móveis](https://github.com/Azure/azure-mobile-services-cordova) ao seu projeto. Esse plug-in dá suporte a [notificações por push](#push-notifications).

No editor, abra ou crie um arquivo JavaScript e adicione o código a seguir que define a variável `MobileServiceClient` e fornece a URL e a chave do aplicativo do serviço móvel no construtor `MobileServiceClient`, nessa ordem.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Você deve substituir o espaço reservado `AppUrl` pela URL do aplicativo do seu serviço móvel e `AppKey` pela chave do aplicativo. Para saber como obter a URL do aplicativo e a chave do aplicativo para o serviço móvel, confira o tutorial [Adicionar os Serviços Móveis a um aplicativo existente](mobile-services-html-get-started-data.md).

>[AZURE.IMPORTANT]A chave do aplicativo serve para filtrar solicitações aleatórias de seu serviço móvel e é distribuída com o aplicativo. Como essa chave não é criptografada, ela não pode ser considerado segura. Para realmente proteger o acesso aos seus dados de serviços móveis, você deve autenticar os usuários antes de permitir o acesso. Para saber mais, confira [Como autenticar usuários](#caching).

##<a name="querying"></a>Como consultar dados de um serviço móvel

Todos os códigos que acessam e modificam dados na tabela de Banco de Dados SQL chamam funções no objeto `MobileServiceTable`. Você obtém uma referência à tabela chamando a função `getTable()` em uma instância do `MobileServiceClient`.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Como filtrar dados retornados

O código a seguir ilustra como filtrar dados incluindo uma cláusula `where` em uma consulta. Ele retorna todos os itens da `todoItemTable` cujo campo complete é igual a `false`. `todoItemTable` é a referência à tabela de serviços móveis que criamos anteriormente. A função where aplica um predicado de filtragem de linha à consulta na tabela. Ela aceita como argumento um objeto JSON ou uma função que define o filtro de linha e retorna uma consulta que pode ser composta adicionalmente.

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

A instrução `where` e a consulta SQL acima encontraram itens incompletos atribuídos a "david" de dificuldade “média”.

No entanto, há outra maneira de escrever a mesma consulta. Uma chamada `.where` no objeto Query adicionará uma expressão `AND` à cláusula `WHERE`; portanto, poderíamos ter escrito isso em três linhas em vez de:

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

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável. Todas as chamadas `where` até agora introduzem um objeto com alguns parâmetros e são comparadas quanto à igualdade com os dados do banco de dados. No entanto, há outra sobrecarga para o método de consulta, que assume uma função em vez do objeto. Nesta função podemos então escrever expressões mais complexas, usando operadores tais como desigualdade e outras operações relacionais. Nessas funções, a palavra-chave `this` vincula-se ao objeto do servidor.

O corpo da função é traduzido em uma expressão booleana OData (Open Data Protocol) que é passada para um parâmetro de cadeia de caracteres de consulta. É possível passar em uma função que não recebe parâmetros, da seguinte forma:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Ao passar uma função com parâmetros, qualquer argumento após a cláusula `where` é associado aos parâmetros da função na ordem. Quaisquer objetos que vêm de fora do escopo da função DEVEM ser passados como parâmetros - a função não pode capturar quaisquer variáveis externas. Nos próximos dois exemplos, o argumento "david" está associado ao parâmetro `name` e, no primeiro exemplo, o argumento “médio" também está associado ao parâmetro `level`. Além disso, a função deve consistir em uma única instrução `return` com uma expressão com suporte da seguinte forma:

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

É possível combinar `where` com `orderBy`, `take` e `skip`. Consulte a próxima seção para obter detalhes.

### <a name="sorting"></a>Como classificar dados retornados

O código a seguir ilustra como classificar dados incluindo uma função `orderBy` ou `orderByDescending` na consulta. Ele retorna os itens da `todoItemTable` classificada em ordem crescente pelo campo `text`. Por padrão, o servidor retorna apenas os 50 primeiros elementos.

> [AZURE.NOTE]Um tamanho da página de servidor controlado por nós usado por padrão para prevenir todos os elementos de serem retornados. Isso impede que solicitações padrão de grandes conjuntos de dados impactem negativamente o serviço. Você pode aumentar o número de itens a ser retornado pela chamada `take` conforme descrito na próxima seção. `todoItemTable` é a referência à tabela do serviço móvel que criamos anteriormente.

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

Por padrão, os Serviços Móveis retornam apenas 50 linhas em uma determinada solicitação, a menos que o cliente solicite explicitamente mais dados na resposta. O código a seguir mostra como implementar a paginação de dados retornados usando as cláusulas `take` e `skip` na consulta. A consulta a seguir, quando executada, retorna os três itens principais na tabela.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Observe que o método `take(3)` foi convertido na opção de consulta `$top=3` no URI da consulta.

A seguinte consulta revisada ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Novamente, você pode ver a URI da solicitação enviada ao serviço móvel. Observe que o método `skip(3)` foi movido para a opção de consulta `$skip=3` no URI da consulta.

Esse é um cenário simplificado de passagem de valores de paginação embutidos em código para as funções `take` e `skip`. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores.

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

A função `lookup` usa apenas o valor `id` e retorna o objeto do banco de dados com essa ID. Tabelas de banco de dados são criadas com uma coluna de números inteiros ou de cadeia de caracteres `id`. Uma coluna de cadeia de caracteres `id` é o padrão.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Executar uma operação de consulta OData

Os Serviços Móveis usam as convenções de URI de consulta OData para compor e executar as consultas REST. Nem todas as consultas OData podem ser compostas usando as funções de consulta integradas, especialmente operações de filtro complexas como pesquisar por uma subcadeia em uma propriedade. Para esses tipos de consultas complexas, você pode passar qualquer cadeia de caracteres de opção de consulta do OData para a função `read` como mostrado abaixo:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Quando você fornece uma cadeia de caracteres de opção de consulta do OData bruta para a função `read`, não pode também usar os métodos do construtor de consultas na mesma consulta. Neste caso, você deve compor toda sua consulta como uma cadeia de caracteres de consulta do OData. Para obter mais informações sobre as opções de consulta do sistema OData, consulte a [referência de opções de consulta do sistema OData].

##<a name="inserting"></a>Como inserir dados em um serviço móvel

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

###Trabalhando com valores de ID

Os Serviços Móveis oferecem suporte a valores exclusivos e personalizados de cadeia de caracteres para a coluna **id** da tabela. Isso permite que aplicativos usem valores personalizados, como endereços de email ou nomes de usuário para a ID. Por exemplo, o código abaixo insere um novo item como um objeto JSON, onde a ID exclusiva é um endereço de email:

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

IDs de cadeia de caracteres fornecem os seguintes benefícios:

+ As IDs são geradas sem fazer uma viagem ida e volta ao banco de dados.
+ Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
+ Os valores de IDs podem integrar-se melhor a uma lógica do aplicativo.

Quando um valor de ID de cadeia de caracteres não está definido em um registro inserido, os Serviços Móveis geram um valor exclusivo para a ID. Para saber mais sobre como gerar seus próprios valores de ID, no cliente ou em um back-end do .NET, confira [Como gerar valores de ID exclusivos](mobile-services-how-to-use-server-scripts.md#generate-guids).

Você também pode usar IDs de números inteiros para suas tabelas. Para usar uma ID de número inteiro, você deve criar sua tabela com o comando `mobile table create` usando a opção `--integerId`. Esse comando é usado com a CLI (interface de linha de comando) para Azure. Para obter mais informações sobre como usar a CLI, consulte [CLI para gerenciar tabelas de Serviços Móveis](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Como modificar dados em um serviço móvel

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

##<a name="deleting"></a>Como excluir dados em um serviço móvel

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

##<a name="binding"></a>Como exibir dados na interface do usuário

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

##<a name="#custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON. Para um exemplo completo, incluindo como criar uma API personalizada em seu serviço móvel, consulte [Chamar uma API personalizada do cliente].

Você chama uma API personalizada do cliente chamando o método [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) em **MobileServiceClient**. Por exemplo, a linha de código a seguir envia uma solicitação POST à API **completeAll** no serviço móvel local:

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });

 
Para exemplos mais realistas e uma discussão mais abrangente do **InvokeApi**, confira [API personalizada nos SDKs do cliente Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

##<a name="caching"></a>Como autenticar usuários

Os Serviços Móveis oferecem suporte a autenticação e a autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: o Facebook, o Google, a Conta da Microsoft e o Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

>[AZURE.NOTE]Ao usar a autenticação em um aplicativo PhoneGap ou Cordova, você também deve adicionar os seguintes plug-ins ao projeto:
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


Há dois fluxos de autenticação com suporte: um fluxo de _servidor_ e um fluxo de _cliente_. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

###Fluxo de servidor
Para que os Serviços Móveis gerenciem o processo de autenticação em seu aplicativo da Windows Store ou HTML5, você deve registrar seu aplicativo no provedor de identidade. Em seguida, no seu serviço móvel, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo](mobile-services-html-get-started-users.md).

Depois de registrar o provedor de identidade, basta chamar o método [LoginAsync] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, para fazer logon com o Facebook, use o código a seguir.

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Se estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o método `login` acima para um dos seguintes: `microsoftaccount`, `facebook`, `twitter`, `google` ou `windowsazureactivedirectory`

Nesse caso, os Serviços Móveis gerenciam o fluxo de autenticação OAuth 2.0 exibindo a página de logon do provedor selecionado e gerando um token de autenticação dos Serviços Móveis depois de um logon bem-sucedido com o provedor de identidade. A função [login], quando concluída, retorna um objeto JSON (**user**) que expõe a ID do usuário e o token de autenticação dos Serviços Móveis nos campos **userId** e **authenticationToken**, respectivamente. Esse token pode ser armazenado em cache e reutilizado até que expire. Para obter mais informações, consulte Armazenando o [token de autenticação em cache].

> [AZURE.NOTE]**Aplicativo da Windows Store** Ao usar o provedor de logon da Conta da Microsoft para autenticar usuários de seu aplicativo da Windows Store, você também deve registrar o pacote de aplicativos nos Serviços Móveis. Ao registrar as informações do pacote de aplicativos da Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experiência de logon única. Se você não fizer isso, os usuários de login da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Para saber como registrar seu pacote de aplicativos da Windows Store, consulte [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Após as informações do pacote serem registradas nos Serviços Móveis, chame o método [login](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank") fornecendo um valor de **true** para o parâmetro <em>useSingleSignOn</em> para reutilizar as credenciais.

###Fluxo de cliente
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

Esse exemplo pressupõe que o token fornecido pelo respectivo SDK do provedor é armazenado na variável `token`. O Twitter não pode ser usado para autenticação de cliente desta vez. O Microsoft Azure Active Directory não pode ser usado com back-ends do JavaScript para autenticação de cliente no momento.

###Armazenando o token de autenticação em cache
Em alguns casos, a chamada para o método login pode ser evitada após a primeira vez que o usuário é autenticado. Podemos usar [sessionStorage] ou [localStorage] para armazenar em cache a identidade do usuário quando fizerem o login pela primeira vez e todas as vezes subsequentes que verificamos se já temos a identidade do usuário armazenada em cache. Se o cache estiver vazio ou a chamada falhar (ou seja, a sessão de logon atual tiver expirado), ainda precisaremos passar pelo processo de logon.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;

##<a name="push-notifications"></a>Como registrar notificações por push

Quando seu aplicativo é um aplicativo PhoneGap ou Apache Cordova HTML/JavaScript, a plataforma móvel nativa permite receber notificações por push no dispositivo. O [plug-in Apache Cordova para os Serviços Móveis do Azure](https://github.com/Azure/azure-mobile-services-cordova) permite que você se registre para notificações por push nos Hubs de notificação do Azure. O serviço de notificação específico usado depende da plataforma de dispositivo nativa na qual o código é executado. Para obter um exemplo de como fazer isso, confira a amostra [Usar o Microsoft Azure para notificações por push em aplicativos Cordova](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle).

>[AZURE.NOTE]Este plug-in atualmente dá suporte apenas a dispositivos iOS e Android. Para uma solução que também inclui dispositivos do Windows, confira o artigo [Notificações por Push para aplicativos PhoneGap usando a integração com Hubs de Notificação](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx).

##<a name="errors"></a>Como tratar erros

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

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>Como usar promessas

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

A promessa `then` é igual a promessa `done`, mas ao contrário da promessa `then`, `done` gera qualquer erro que não é tratado dentro da função. Se você não fornecer um manipulador de erro para `then` e a operação tiver um erro, ela não gerará uma exceção, mas, em vez disso, retornará uma promessa no estado de erro. Para obter mais informações, consulte [then].

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Desta forma:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Você pode usar promessas de várias maneiras diferentes. Você pode encadear operações de promessas chamando `then` ou `done` na promessa que é retornada pela função anterior `then`. Use `.then().then()` para um estágio intermediário da operação (por exemplo, `then`) e `done` para o estágio final da operação (por exemplo, `.then().then().done()`). Você pode encadear várias funções `then` porque `then` retorna uma promessa. Não é possível encadear mais de um método `done` porque ele retorna indefinido. [Saiba mais sobre as diferenças entre then e done]

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>Como personalizar cabeçalhos de solicitações de clientes

Você pode enviar cabeçalhos de solicitações personalizados usando a função `withFilter`, ler e escrever propriedades arbitrárias da solicitação prestes a ser enviada com o filtro. Talvez você queira adicionar um cabeçalho personalizado HTTP desse tipo se um script do lado do servidor precisar ou puder ser melhorado por ele.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

Os filtros são usados para muito mais do que cabeçalhos de solicitações personalizados. Eles podem ser usados para examinar ou alterar solicitações, examinar ou alterar respostas, ignorar chamadas de rede, enviar várias chamadas etc.

##<a name="hostnames"></a>Como usar o compartilhamento de recursos entre origens

Para controlar quais sites podem interagir e enviar solicitações para o seu serviço móvel, não deixe de adicionar o nome do host do site que você usa para hospedá-lo na lista branca do CORS (Compartilhamento de Recursos entre Origens). Para um serviço móvel do back-end de JavaScript, você pode configurar a lista de permissões na guia Configurar no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Você pode usar caracteres curinga se necessário. Por padrão, os novos Serviços Móveis instruem os navegadores para permitirem acesso somente a partir do `localhost`, e o CORS (Compartilhamento de Recursos entre Origens) permite que o código em JavaScript em execução em um navegador em um nome de host externo interaja com o Serviço Móvel. Essa configuração não é necessária para aplicativos WinJS.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #caching
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Saiba mais sobre as diferenças entre then e done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Associação de dados (aplicativos da Windows Store usando JavaScript e HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[Autenticar seu aplicativo com logon único]: mobile-services-windows-store-javascript-single-sign-on.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[referência de opções de consulta do sistema OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502
[Chamar uma API personalizada do cliente]: mobile-services-html-call-custom-api.md
 

<!---HONumber=July15_HO4-->