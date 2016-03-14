<properties
	pageTitle="Visualização do Azure Active Directory B2C: usar a API do Graph | Microsoft Azure"
	description="Como chamar a API do Graph para um locatário B2C usando uma identidade de aplicativo para automatizar o processo."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Visualização do B2C do Azure AD: usar a API do Graph


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Os locatários do Azure Active Directory (Azure AD) B2C tendem a ser muito grandes. Isso significa que muitas tarefas comuns de gerenciamento de locatário devem ser executadas programaticamente. O principal exemplo é o gerenciamento de usuário. Talvez seja necessário migrar o repositório de usuário existente para um locatário B2C. Talvez seja melhor hospedar o registro de usuário em sua própria página e criar contas de usuário no Azure AD nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode realizar essas tarefas usando a API do Graph do Azure AD.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Para locatários B2C, existem basicamente dois modos de se comunicar com a API do Graph.

- Para tarefas de execução única, interativas, você deverá agir como uma conta de administrador no locatário B2C quando executar as tarefas. Esse modo requer que um administrador entre com credenciais antes de poder executar todas as chamadas à API do Graph.
- Para tarefas automatizadas, contínuas, você deve usar algum tipo de conta de serviço fornecida com os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso registrando um aplicativo e autenticando no Azure AD. Isso é feito usando uma **ID do aplicativo** que usa a [concessão de credenciais do cliente OAuth 2.0](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Nesse caso, o aplicativo atua em nome próprio, e não como usuário, para chamar a API do Graph.

Neste artigo, discutiremos como executar o caso de uso automatizado. Para demonstrar isso, vamos criar um .NET 4.5 `B2CGraphClient` que executa as operações CRUD (criar, ler, atualizar e excluir) do usuário. O cliente terá uma CLI (interface de linha de comando) do Windows que permite que você chame vários métodos. No entanto, o código é gravado para se comportar de forma não interativa e automatizada.

## Obter um locatário do Azure AD B2C

Antes de criar o aplicativo ou os usuários ou interagir com o Azure AD, você precisará de um locatário habilitado para B2C e uma conta de administrador global no locatário. Se você ainda não tiver um locatário, confira a [introdução ao AD B2C do Azure](active-directory-b2c-get-started.md).

## Registrar um aplicativo de serviço em seu locatário

Agora que tem um locatário B2C, você precisa criar seu aplicativo de serviço usando os cmdlets do PowerShell do Azure AD. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Para usar a API do Graph com seu locatário B2C, você precisará registrar um aplicativo dedicado usando o PowerShell. Siga as instruções neste artigo para fazer isso. Você não pode usar seus aplicativos B2C já existentes que registrou no Portal do Azure novamente. Essa é uma limitação da visualização do AD B2C do Azure que deve ser removida em um futuro próximo. Estamos planejando atualizar esse artigo quando isso acontecer.

Depois de instalar o módulo do PowerShell, abra o PowerShell e conecte-se ao locatário B2C. Depois de executar o `Get-Credential`, você será solicitado a fornecer um nome de usuário e uma senha. Insira o nome de usuário e a senha da sua conta de administrador do locatário B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de criar o aplicativo, você precisará gerar um novo **segredo do cliente**. O aplicativo usará o segredo do cliente para se autenticar no Azure AD e adquirir tokens de acesso. Você pode gerar um segredo válido no PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

O comando final deve fornecer o novo segredo do cliente. Copie-o em local seguro. Você precisará dela mais tarde. Agora você pode criar o aplicativo, fornecendo o novo segredo do cliente como uma credencial para o aplicativo:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Se a criação do aplicativo for bem-sucedida, ela deverá indicar as propriedades do aplicativo, como as mostradas acima. Você precisará tanto da `ObjectId` quanto da `AppPrincipalId`; portanto, copie esses valores também.

Agora que criou um aplicativo no locatário B2C, você precisa atribuir a ele as permissões necessárias para realizar operações de CRUD do usuário. Atribua as três funções de aplicativo: leitores de diretório (para ler usuários), gravadores de diretório (para criar e atualizar usuários) e um administrador da conta de usuário (para excluir os usuários). Essas funções têm identificadores conhecidos, você pode substituir o parâmetro `-RoleMemberObjectId` com a `ObjectId` acima e executar os comandos a seguir. Para ver a lista de todas as funções de diretório, tente executar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Agora, você tem um aplicativo que tem permissão para criar, ler, atualizar e excluir usuários do locatário B2C.

## Baixar, configurar e compilar o código de exemplo

Primeiro, baixe o código de exemplo e execute-o. Em seguida, faremos uma análise mais detalhada. Você pode [baixar o código de exemplo como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Você também pode cloná-lo em um diretório de sua escolha:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra a solução do Visual Studio `B2CGraphClient\B2CGraphClient.sln` no Visual Studio. No `B2CGraphClient` de projeto, abra o arquivo `App.config`. Substitua as três configurações do aplicativo por seus próprios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Agora, clique com o botão direito do mouse na solução `B2CGraphClient` e compile novamente o exemplo. Se você tiver êxito, deverá ter agora um arquivo executável `B2C.exe` localizado em `B2CGraphClient\bin\Debug`.

## Criar operações CRUD do usuário usando a API do Graph

Para usar o B2CGraphClient, abra um prompt de comando `cmd` do Windows e altere o diretório para o diretório `Debug`. Em seguida, execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Isso exibirá uma breve descrição de cada comando. Cada vez que você chamar um desses comandos, o `B2CGraphClient` faz uma solicitação para a API do Azure AD Graph.

### Obter um token de acesso

Qualquer solicitação para a API do Graph requer um token de acesso para autenticação. O `B2CGraphClient` usa a ADAL (Biblioteca de autenticação do Active Directory) aberta para ajudar a obter tokens de acesso. A ADAL facilita a aquisição de tokens, fornecendo uma API simples e cuidando de alguns detalhes importantes, como armazenar em cache os tokens de acesso. No entanto, você não precisa usar a ADAL para obter tokens. Você também pode obter tokens ao criar solicitações HTTP.

> [AZURE.NOTE]
	Esse exemplo de código usa a ADAL v2, a versão da ADAL com disponibilidade geral. Ele Não usa a ADAL v4, que é uma versão de visualização para trabalhar com o B2C do Azure AD. Para a visualização do B2C do AD do Azure, você deve usar a ADAL v2 para se comunicar com a Graph API. Ao longo do tempo, esperamos fornecer acesso à API do Graph com a ADAL v4, para que você não precise usar duas versões diferentes da ADAL em sua solução AD B2C do Azure completa.

Quando `B2CGraphClient` é executado, o código cria uma instância da classe `B2CGraphClient`. O construtor para essa classe configura a estrutura de autenticação da ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos usar o comando `B2C Get-User` como um exemplo. Quando `B2C Get-User` é invocado sem quaisquer entradas adicionais, o CLI chama o `B2CGraphClient.GetAllUsers(...)` método. Este método chama `B2CGraphClient.SendGraphGetRequest(...)`, que envia uma solicitação HTTP GET para a Graph API. Antes de `B2CGraphClient.SendGraphGetRequest(...)` enviar a solicitação GET, ele obtém primeiro um token de acesso usando a ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Você pode obter um token de acesso para a API do Graph chamando o método ADAL `AuthenticationContext.AcquireToken(...)`. A ADAL, em seguida, retorna um `access_token` que representa a identidade do aplicativo.

### Ler usuários

Quando você quiser obter uma lista de usuários ou obter um usuário específico da API do Graph, poderá enviar uma solicitação HTTP `GET` para o ponto de extremidade `/users`. Uma solicitação para todos os usuários em um locatário tem esta aparência:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```
 > B2C Get-User
 ```

Há dois aspectos importantes a serem observados:

- O token de acesso adquirido pela ADAL foi adicionado ao `Authorization` cabeçalho usando o esquema `Bearer`.
- Para locatários B2C, você deve usar o parâmetro de consulta `api-version=beta`.


> [AZURE.NOTE]
	A versão beta da Graph API do AD do Azure fornece funcionalidade de visualização. Consulte [esta postagem de blog da equipe de API do Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) para obter detalhes sobre a versão beta.

Ambos esses detalhes são tratados no `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Criar contas de usuário consumidor

Ao criar contas de usuário no seu locatário B2C, você pode enviar uma solicitação HTTP `POST` para o ponto de extremidade `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Todas as propriedades nesta solicitação são obrigatórias para criar usuários consumidores. Os comentários `//` foram incluídos para fins de ilustração. Não os inclua em uma solicitação real.

Para ver a solicitação, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O comando `Create-User` usa um arquivo. JSON como parâmetro de entrada. Ele contém uma representação JSON de um objeto de usuário. Existem dois arquivos. JSON de exemplo no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos necessários acima, há vários campos opcionais incluídos nesses arquivos que você pode usar. Detalhes sobre esses outros campos podem ser encontrados na [Referência de entidade da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Você pode ver como a solicitação POST é construída em `B2CGraphClient.SendGraphPostRequest(...)`.

- Ela anexa um token de acesso ao cabeçalho `Authorization` da solicitação.
- Ela define `api-version=beta`.
- Ela inclui o objeto de usuário JSON no corpo da solicitação.

### Atualizar contas de usuário consumidor

Quando você atualiza os objetos de usuário, o processo é semelhante àquele usado para criar objetos de usuário. Mas esse processo usa o método HTTP `PATCH`:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Tente atualizar um usuário atualizando os arquivos JSON com novos dados. Você pode usar o `B2CGraphClient` para executar um destes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

### Excluir usuários

O processo de exclusão de um usuário é simples. Use o método HTTP `DELETE` e construa a URL com a ID de objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, insira esse comando e exiba a solicitação Delete exclusão resultante que é mostrada no console:

```
> B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

Você pode executar outras ações com a API do Graph do Azure AD além do gerenciamento de usuários. A [Referência da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece os detalhes de cada ação, além das solicitações de amostra.

## Usar atributos personalizados

Quase todos os aplicativos consumidores precisam armazenar algum tipo de informação de perfil de usuário personalizada. Uma maneira de fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma maneira que trata qualquer outra propriedade em um objeto de usuário. Você pode atualizar, excluir ou consultar o atributo, enviá-lo como uma declaração de entrada em tokens e assim por diante.

Para definir um atributo personalizado em seu locatário B2C, confira a [referência de atributo personalizado da visualização do B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos no locatário B2C usando o`B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída dessas funções revela os detalhes de cada atributo personalizado, como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade em seus objetos de usuário. Atualize seu arquivo .json com a nova propriedade e um valor para a propriedade e execute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Ao usar o `B2CGraphClient`, você tem um aplicativo de serviço que pode gerenciar os usuários de locatário B2C programaticamente. O `B2CGraphClient` usa sua própria identidade de aplicativo para autenticar na API do Azure AD Graph. Ele também adquire tokens usando um segredo do cliente. Ao incorporar essa funcionalidade a seu aplicativo, lembre-se de alguns pontos importantes para aplicativos B2C:

- Você precisa conceder ao aplicativo as permissões apropriadas no locatário.
- Por enquanto, você precisa usar a versão 2 da ADAL para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
- Quando você chamar a API do Graph, use [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Quando você cria e atualiza usuários consumidores, algumas propriedades são obrigatórias, conforme descrito acima.

> [AZURE.IMPORTANT] Você precisa levar em conta as características de replicação do serviço de diretório subjacente do AD B2C do Azure quando usa a API do Azure AD Graph em seu aplicativo B2C. (Leia [este artigo](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) para saber mais). Depois que um cliente se inscrever para seu aplicativo B2C usando uma política de **Inscrição**, se você tentar ler o objeto do usuário usando a API do Graph do Azure AD em seu aplicativo, ela poderá não estar disponível. Você precisa aguardar alguns segundos para que o processo de replicação seja concluído. Temos a intenção de publicar orientações mais concretas sobre a "garantia de consistência de leitura-gravação" fornecida pela API do Graph do Azure AD e o serviço de diretório na fase de disponibilidade geral.

Se você tiver perguntas ou solicitações de ações que deseja executar usando a API do Graph em seu locatário B2C, deixe um comentário sobre este artigo ou apresente um problema no repositório de código de exemplo do GitHub.

<!---HONumber=AcomDC_0302_2016-->