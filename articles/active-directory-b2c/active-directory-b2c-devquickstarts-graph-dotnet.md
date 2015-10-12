<properties
	pageTitle="Visualização do B2C do AD do Azure | Microsoft Azure"
	description="Como chamar a Graph API para um diretório B2C usando uma identidade de aplicativo para automatizar o processo."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>


# Visualização do B2C do AD do Azure: usando o Graph API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Os diretórios do B2C do AD do Azure tendem a ser muito grandes, o que significa que muitas tarefas comuns de gerenciamento de diretório precisam ser executadas de forma programática. Um exemplo principal é o gerenciamento de usuários. Talvez seja necessário migrar o repositório de usuários existente para um diretório B2C ou talvez você queira hospedar o registro de usuários em sua própria página, criando contas de usuário no AD do Azure nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário, o que pode ser feito usando a Graph API do AD do Azure.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
Para diretórios B2C, existem basicamente dois modos de se comunicar com a Graph API.

- Para tarefas interativas de execução única, provavelmente você desejará executar tarefas de gerenciamento atuando como uma conta de administrador no diretório B2C. Esse modo exige que um administrador faça logon com suas credenciais antes de executar chamadas à Graph API.
- Para tarefas automatizadas e contínuas, convém executar tarefas de gerenciamento usando algum tipo de conta de serviço à qual você concede os privilégios necessários. No AD do Azure, você pode fazer isso registrando um aplicativo e autenticando-se no AD do Azure usando uma "identidade de aplicativo", usando a [Concessão de Credenciais de Cliente OAuth 2.0](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Nesse caso, o aplicativo chama a Graph API atuando como si mesmo, em vez de um usuário específico.  

Neste artigo, mostraremos como executar o segundo caso, de uso automatizado. Para demonstrá-lo, vamos criar um "B2CGraphClient" do .NET 4.5, que executa operações CRUD do usuário. Para fins de experiência, o cliente terá uma interface de linha de comando do Windows que permite que você invoque vários métodos. No entanto, o código é gravado para se comportar de forma não interativa e automatizada. Vamos começar.

## Obter um diretório habilitado para B2C

Antes de criar o aplicativo ou os usuários ou interagir com o AD do Azure, você precisará de um diretório habilitado para B2C e uma conta de administrador nesse diretório. Se você não tiver uma, siga o guia de [introdução ao B2C do AD do Azure](active-directory-b2c-get-started.md).

## Registrar um aplicativo de serviço em seu diretório

Agora que tem um diretório B2C, você precisa criar seu aplicativo de serviço usando os Cmdlets do Powershell do AD do Azure. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Em seguida, você pode baixar e instalar o [Módulo do Active Directory do Azure de 64 bits para Windows Powershell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]Para usar a Graph API com seu diretório B2C, você precisará registrar um aplicativo dedicado usando o powershell, seguindo estas instruções. Você não pode usar seus aplicativos B2C já existentes que você registrou no Portal do Azure novamente. Essa é uma limitação do modo de visualização do AD B2C do Azure que será removida em breve - nesse ponto, atualizaremos este artigo.

Depois de instalar o módulo do Powershell, abra o Powershell e conecte-se ao diretório B2C. Após a execução `Get-Credential`, será solicitado o seu nome de usuário e uma senha - Digite os da sua conta de administrador do diretório B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

Antes de criar o aplicativo, você precisa gerar um novo "segredo do cliente". O aplicativo usará o segredo do cliente para se autenticar no AD do Azure e adquirir tokens de acesso. Você pode gerar um segredo válido no Powershell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

O comando final acima deve fornecer o novo segredo do cliente. Copie-o em algum lugar seguro. Você precisará dele novamente em breve. Agora você pode criar o aplicativo, fornecendo o novo segredo do cliente como uma credencial para o aplicativo:

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

Se a criação do aplicativo for bem-sucedida, ela deverá indicar algumas propriedades do aplicativo, como as mostradas acima. Você precisará de ambos os `ObjectId` e `AppPrincipalId`, então copie esses valores também.

Agora que criou um aplicativo no diretório B2C, você precisa atribuir a ele as permissões necessárias para realizar operações de CRUD do usuário. Você precisará atribuir três funções diferentes ao aplicativo: Leitores de Diretório (para ler usuários), Gravadores de Diretório (para criar e atualizar usuários) e Administrador de Contas de Usuários (para excluir usuários). Essas funções têm identificadores conhecidos, você pode executar os comandos abaixo, substituindo o `-RoleMemberObjectId` parâmetro com o `ObjectId` de acima. Para ver a lista de todas as funções de diretório, tente executar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

Agora, você tem um aplicativo que tem permissão para criar, ler, atualizar e excluir usuários do diretório B2C. Vamos escrever um código que o usa.

## Baixar, configurar e compilar o código de exemplo

Primeiro, vamos baixar o código de exemplo e executá-lo. Em seguida, podemos conferir o que está acontecendo nos bastidores. Você pode [baixar o código de exemplo como. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) ou cloná-lo em um diretório de sua escolha:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra o `B2CGraphClient\B2CGraphClient.sln` solução do Visual Studio no Visual Studio. No `B2CGraphClient` de projeto, abra o arquivo `App.config`. Substitua as três configurações do aplicativo por seus próprios valores, da seguinte forma:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Agora, clique com o botão direito do mouse na `B2CGraphClient` solução e compile novamente o exemplo. Se tiver êxito, você deve ter agora um executável `B2C.exe` localizado em `B2CGraphClient\bin\Debug`.

## CRUD do usuário com a Graph API

Para usar o B2CGraphClient, abra um prompt de comando do Windows cmd e cd para o `Debug` diretório. Em seguida, execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Isso exibirá uma breve descrição de cada comando. Quando você chamar qualquer um desses comandos, o B2CGraphClient fará uma solicitação para a Graph API do AD do Azure.

### Obtendo um token de acesso

Qualquer solicitação à Graph API exigirá um token de acesso para autenticação. O B2CGraphClient usa a ADAL (Biblioteca de Autenticação do Active Directory) de software livre para ajudar a obter tokens de acesso. Você certamente não precisa usar a ADAL para obter tokens. É possível obter tokens criando solicitações HTTP por conta própria. A ADAL facilita um pouco a aquisição de tokens, fornecendo uma API simples e cuidando de alguns detalhes importantes, como armazenar em cache os tokens de acesso.

> [AZURE.NOTE]Este exemplo de código deliberadamente usa a ADAL v2, a versão da ADAL com disponibilidade geral. Ele NÃO usa a ADAL v4, que é uma versão de visualização projetada para trabalhar com o B2C do AD do Azure. Para a visualização do B2C do AD do Azure, você deve usar a ADAL v2 para se comunicar com a Graph API. Ao longo do tempo, habilitaremos o acesso à Graph API com a ADAL v4, para que você não precise usar duas versões diferentes da ADAL em sua solução completa do B2C do AD do Azure.

Quando o B2CGraphClient é executado, ele cria uma instância da `B2CGraphClient` classe. O construtor para essa classe configura a estrutura de autenticação da ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the directory to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are 
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos usar o `B2C Get-User` comando como um exemplo. Quando `Get-User` é invocado sem quaisquer entradas adicionais, o CLI chama o `B2CGraphClient.GetAllUsers(...)` método. Este método chama `B2CGraphClient.SendGraphGetRequest(...)`, que envia uma solicitação HTTP GET para a Graph API. Antes de enviar a solicitação GET, ele obtém primeiro um token de acesso usando a ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	... 

```

Como pode ser visto, você pode obter um token de acesso para a Graph API chamando o método ADAL `AuthenticationContext.AcquireToken(...)`. A ADAL retornará um access\_token que representa a identidade do aplicativo.

### Lendo usuários

Quando você quiser obter uma lista de usuários da Graph API ou obter um usuário específico, você pode enviar uma solicitação HTTP GET para o `/users` ponto de extremidade. Uma solicitação para todos os usuários em um diretório seria semelhante a:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Para ver essa solicitação em ação, tente executar:

 ```
 > B2C Get-User
 ```
 
Há dois aspectos importantes a serem observados aqui:

- O token de acesso adquirido pelo ADAL foi adicionado ao `Authorization` cabeçalho usando o `Bearer` esquema.
- Para diretórios B2C, você deve usar o parâmetro de consulta `api-version=beta`.


> [AZURE.NOTE]A versão beta da Graph API do AD do Azure fornece funcionalidade de visualização. Consulte [esta postagem de blog da equipe de Graph API](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) para obter detalhes sobre a versão beta.

Ambos esses detalhes são tratados no `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...
	
	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}
	
	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);
	
	... 
```
		
### Criando contas de usuário consumidor 

Ao criar contas de usuário no seu diretório B2C, você pode enviar uma solicitação HTTP POST para o `/users` ponto de extremidade:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.
	 
	"accountEnabled": false,                    // always set to false
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Cada uma das propriedades incluídas na solicitação acima é necessária para criar usuários consumidores. Os `//` comentários foram incluídos para ilustração - não os inclua em uma solicitação real.

Para ver essa solicitação em ação, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando usa um `.json` como um parâmetro de entrada que contém uma representação JSON de um objeto de usuário. Há dois arquivos de exemplo `.json` incluídos no código de exemplo - `usertemplate-email.json` e `usertemplate-username.json` -que você pode modificar para atender às suas necessidades. Além dos campos necessários acima, há vários campos opcionais incluídos nesses arquivos que você pode usar. Detalhes sobre esses outros campos podem ser encontrados na [Referência de entidade da Graph API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Você pode ver como essa solicitação POST é construída em `B2CGraphClient.SendGraphPostRequest(...)`, que:

- Anexa um token de acesso ao `Authorization` cabeçalho da solicitação.
- Conjuntos `api-version=beta`.
- Inclui o objeto de usuário JSON no corpo da solicitação.

### Atualizando contas de usuário do consumidor

A atualização objetos de usuário é muito semelhante à criação de objetos de usuário, mas usa o verbo HTTP PATCH:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

Você pode tentar atualizar um usuário atualizando seus arquivos JSON com novos dados e usando o B2CGraphClient para executar um dos seguintes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
	
Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

### Excluindo usuários

A exclusão de um usuário é simples: basta usar o verbo HTTP DELETE e construir a URL com a ID de objeto correta:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, experimente o comando abaixo e exiba a solicitação DELETE exclusão resultante que é mostrada no console:

```
> B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

Há muitas outras ações que podem ser executadas com a Graph API do AD do Azure além do gerenciamento de usuários. A [Referência da Graph API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece os detalhes de cada ação, além de solicitações de amostra.


## Usando atributos personalizados

Quase todos os aplicativos de consumidor precisam armazenar algum tipo de informações de perfil de usuário personalizadas. Uma maneira de fazer isso é definir um atributo personalizado no diretório B2C, que permitirá que você trate esse atributo como qualquer outra propriedade em um objeto de usuário. Você pode atualizar, excluir ou consultar o atributo, enviá-lo como uma declaração de entrada em tokens e assim por diante.

Para definir um atributo personalizado em seu diretório B2C, consulte a [referência de atributo personalizado da visualização do B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos no diretório B2C usando o B2CGraphClient:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída dessas funções revelará os detalhes de cada atributo personalizado, como:

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

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` como uma propriedade em seus objetos de usuário. Basta atualizar para o seu `.json` arquivo a nova propriedade, um valor para a propriedade e executar:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

E pronto! Com o B2CGraphClient, agora você tem um aplicativo de serviço que pode gerenciar os usuários do diretório B2C de forma programática. Ele usa sua própria identidade de aplicativo para autenticar a Graph API do AD do Azure e adquire tokens usando um client\_secret. Ao incorporar essa funcionalidade a seu próprio aplicativo, lembre-se de alguns pontos importantes para aplicativos B2C:

- Você precisa conceder ao aplicativo as permissões apropriadas no diretório
- Por enquanto, você precisa usar a ADAL v2 para obter tokens de acesso (ou você pode enviar mensagens do protocolo diretamente, sem uma biblioteca)
- Ao chamar o Graph API, use [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Ao criar e atualizar usuários consumidores, há algumas propriedades necessárias, descritas acima.

Se você tiver perguntas ou solicitações de ações que deseja executar com a Graph API no diretório B2C, entre em contato conosco. Deixe um comentário sobre o artigo ou um registre o problema no repositório GitHub de exemplos de código.

<!---HONumber=Oct15_HO1-->