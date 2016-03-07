<properties
	pageTitle="Como usar o Plug-in do Apache Cordova para os Aplicativos Móveis do Azure"
	description="Como usar o Plug-in do Apache Cordova para os Aplicativos Móveis do Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Como usar a biblioteca de clientes do Apache Cordova para os Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [Plug-in do Apache Cordova para os Aplicativos Móveis do Azure]. Se você for novo nos Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end, criar uma tabela e baixar um projeto Apache Cordova pré-criado. Neste guia, abordaremos o Plug-in do Apache Cordova do lado do cliente.

##<a name="Setup"></a>Configuração e Pré-requisitos

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais. Este guia também pressupõe que você adicionou o Plug-in do Apache Cordova ao seu código. Se você não tiver feito isso, você pode adicionar o plug-in do Apache Cordova ao seu projeto na linha de comando:

```
cordova plugin add ms-azure-mobile-apps
```

Para saber mais sobre como criar [seu primeiro aplicativo do Apache Cordova], consulte a documentação.

##<a name="create-client"></a>Como: criar o cliente

Crie uma conexão de cliente por meio da criação de um objeto `WindowsAzure.MobileServicesClient`. Substitua `appUrl` pela URL de seu aplicativo móvel.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Como criar uma referência de tabela

Para acessar ou atualizar dados, crie uma referência à tabela de back-end. Substitua `tableName` pelo nome da sua tabela

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Como consultar uma referência de tabela

Depois que você tiver uma referência de tabela, será possível usá-la para consultar dados no servidor. As consultas são feitas em uma linguagem "parecida com LINQ". Para retornar todos os dados da tabela, use o seguinte:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

A função de sucesso é chamada com os resultados. Não use `for (var i in results)` na função de sucesso, pois isso causará a iteração de informações incluídas nos resultados quando outras funções de consulta (como `.includeTotalCount()`) forem usados.

Para saber mais sobre a sintaxe Query, confira a [documentação do objeto Query].

### Filtragem de dados no servidor

Você pode usar uma cláusula `where` na referência de tabela:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Você também pode usar uma função que filtra o objeto. Nesse caso, a variável `this` é atribuída ao objeto atual que está sendo filtrado. Veja a seguir uma funcionalidade equivalente ao exemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Como paginar os dados

Utilize os métodos take() e skip(). Por exemplo, se você quiser dividir a tabela em registros de 100 linhas:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

O método `.includeTotalCount()` é usado para adicionar um campo totalCount ao objeto de resultados. O campo totalCount é preenchido com o número total de registros que retornariam se nenhuma paginação fosse usada.

Depois, você pode usar a variável de páginas e alguns botões de interface do usuário para fornecer uma lista de páginas; use loadPage() para carregar os novos registros de cada página. Você deve implementar algum tipo de cache para agilizar o acesso a registros que já foram carregados.


###<a name="sorting-data"></a>Como retornar dados classificados

Use os métodos de consulta .orderBy() ou .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para saber mais sobre o objeto Query, confira a [documentação do objeto Query].

##<a name="inserting"></a>Como inserir dados

Crie um objeto JavaScript com a data adequada e chame table.insert() de maneira assíncrona:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Após a inserção bem-sucedida, o item inserido retorna com os campos adicionais necessários para as operações de sincronização. Você deve atualizar seu próprio cache com essas informações para favorecer as atualizações posteriores.

Observe que o SDK de Node.js Server dos Aplicativos Móveis do Azure dá suporte ao esquema dinâmico para fins de desenvolvimento. No caso do esquema dinâmico, o esquema da tabela é atualizado dinamicamente, permitindo que você adicione colunas à tabela apenas especificando-as em uma operação de inserção ou atualização. Recomendamos que você desative o esquema antes de mover seu aplicativo para produção.

##<a name="modifying"></a>Como modificar dados

Assim como o método .insert(), você deve criar um objeto de atualização e, em seguida, chamar .update(). O objeto de atualização deve conter a ID do registro a ser atualizada. Isso é obtido ao ler o registro ou ao chamar .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Como excluir dados

Chame o método .del() para excluir um registro. Passe a ID em uma referência de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Como autenticar usuários

O Serviço de Aplicativo do Azure oferece suporte à autenticação e autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

Ao usar a autenticação em um aplicativo Apache Cordova, os seguintes plugins Cordova devem estar disponíveis:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Dois fluxos de autenticação são suportados: um server flow e um client flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

##<a name="server-auth"></a>Como autenticar com um provedor (fluxo de servidor)

Para que os Serviços Móveis gerenciem o processo de autenticação em seu aplicativo da Windows Store ou HTML5, você deve registrar seu aplicativo no provedor de identidade. Em seguida, no Serviço de Aplicativo do Azure, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor. Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo].

Depois de registrar seu provedor de identidade, basta chamar o método .login() com o nome de seu provedor. Por exemplo, para fazer logon com o Facebook, use o código a seguir.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o método de login acima para um dos seguintes: `microsoftaccount`, `facebook`, `twitter`, `google` ou `aad`.

Nesse caso, o Serviço de Aplicativo do Azure gerencia o fluxo de autenticação OAuth 2.0 exibindo a página de logon do provedor selecionado e gerando um token de autenticação do Serviço de Aplicativo após um logon bem-sucedido com o provedor de identidade. A função de logon, quando concluída, retorna um objeto JSON (user) que expõe a ID do usuário e o token de autenticação do Serviço de Aplicativo nos campos userId e authenticationToken, respectivamente. Esse token pode ser armazenado em cache e reutilizado até que expire.

##<a name="client-auth"></a>Como autenticar com um provedor (fluxo de cliente)

Seu aplicativo também pode entrar em contato de forma independente com o provedor de identidade e fornecer o token retornado ao Serviço de Aplicativo para autenticação. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade.

### Exemplo básico de autenticação social

Este exemplo usa o SDK de cliente do Facebook para a autenticação:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Esse exemplo pressupõe que o token fornecido pelo respectivo SDK do provedor é armazenado na variável 'token'.

### Exemplo de conta da Microsoft

O exemplo a seguir usa o Live SDK, que oferece suporte a logon único para aplicativos da Windows Store, usando a Conta da Microsoft:

```
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
```

Esse exemplo obtém um token do Live Connect, que é fornecido ao seu Serviço de Aplicativo chamando a função de logon.

##<a name="auth-getinfo"></a>Como obter informações sobre o usuário autenticado

As informações de autenticação do usuário atual podem ser obtidas do ponto de extremidade `/.auth/me` usando qualquer método AJAX. Por exemplo, para usar a API de busca:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Você também pode usar jQuery ou outra API AJAX para buscar as informações. Os dados serão recebidos como um objeto JSON.

##<a name="register-for-push"></a>Como registrar para notificações por push

Instale o [phonegap-plugin-push] para manipular notificações por push. Isso pode ser facilmente adicionado usando o comando `cordova plugin add` na linha de comando, ou por meio do instalador de plug-ins Git no Visual Studio. O código a seguir em seu aplicativo Apache Cordova registrará seu dispositivo para notificações por push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use o SDK dos Hubs de Notificação para enviar notificações por push do servidor. Você nunca deve enviar notificações por push diretamente dos clientes, pois isso pode ser usado para disparar um ataque de negação de serviço contra os Hubs de Notificação ou PNS.

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar autenticação ao seu aplicativo]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para os Aplicativos Móveis do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[seu primeiro aplicativo do Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentação do objeto Query]: https://msdn.microsoft.com/pt-BR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->