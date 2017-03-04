---
title: 'Azure Active Directory B2C: usar a API do Graph | Microsoft Docs'
description: "Como chamar a API do Graph para um locatário B2C usando uma identidade de aplicativo para automatizar o processo."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: bryanla
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: a932b617d57184ef714bf18f1e1e23599db52487
ms.lasthandoff: 02/06/2017


---
# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C: usar a API do Graph
Os locatários do Azure Active Directory (Azure AD) B2C tendem a ser muito grandes. Isso significa que muitas tarefas comuns de gerenciamento de locatário devem ser executadas programaticamente. O principal exemplo é o gerenciamento de usuário. Talvez seja necessário migrar o repositório de usuário existente para um locatário B2C. Talvez seja melhor hospedar o registro de usuário em sua própria página e criar contas de usuário no Azure AD nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode realizar essas tarefas usando a API do Graph do Azure AD.

Para locatários B2C, existem basicamente dois modos de se comunicar com a API do Graph.

* Para tarefas de execução única, interativas, você deverá agir como uma conta de administrador no locatário B2C quando executar as tarefas. Esse modo requer que um administrador entre com credenciais antes de poder executar todas as chamadas à API do Graph.
* Para tarefas automatizadas, contínuas, você deve usar algum tipo de conta de serviço fornecida com os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso registrando um aplicativo e autenticando no Azure AD. Isso é feito usando uma **ID de aplicativo** que usa a [concessão de credenciais do cliente OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Nesse caso, o aplicativo atua em nome próprio, e não como usuário, para chamar a API do Graph.

Neste artigo, discutiremos como executar o caso de uso automatizado. Para demonstrar isso, vamos criar um .NET 4.5 `B2CGraphClient` que executa as operações CRUD (criar, ler, atualizar e excluir) do usuário. O cliente terá uma CLI (interface de linha de comando) do Windows que permite que você chame vários métodos. No entanto, o código é gravado para se comportar de forma não interativa e automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um locatário do Azure AD B2C
Antes de criar o aplicativo ou os usuários ou interagir com o Azure AD, você precisará de um locatário habilitado para B2C e uma conta de administrador global no locatário. Se você ainda não tiver um locatário, confira a [introdução ao Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registrar um aplicativo de serviço em seu locatário
Agora que tem um locatário B2C, você precisa criar seu aplicativo de serviço usando os cmdlets do PowerShell do Azure AD.
Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [!IMPORTANT]
> Para usar a API do Graph com seu locatário B2C, você precisará registrar um aplicativo dedicado usando o PowerShell. Siga as instruções neste artigo para fazer isso. Você não pode usar seus aplicativos B2C já existentes que registrou no Portal do Azure novamente.
> 
> 

Depois de instalar o módulo do PowerShell, abra o PowerShell e conecte-se ao locatário B2C. Depois de executar o `Get-Credential`, você será solicitado a fornecer um nome de usuário e uma senha. Insira o nome de usuário e a senha de sua conta de administrador do locatário B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de criar o aplicativo, você precisará gerar um novo **segredo do cliente**.  O aplicativo usará o segredo do cliente para se autenticar no Azure AD e adquirir tokens de acesso. Você pode gerar um segredo válido no PowerShell:

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

Se a criação do aplicativo for bem-sucedida, ela deverá indicar as propriedades do aplicativo, como as mostradas acima. Você precisará da `ObjectId` e da `AppPrincipalId`; portanto, copie esses valores também.

Agora que criou um aplicativo no locatário B2C, você precisa atribuir a ele as permissões necessárias para realizar operações de CRUD do usuário. Atribua as três funções de aplicativo: leitores de diretório (para ler usuários), gravadores de diretório (para criar e atualizar usuários) e um administrador da conta de usuário (para excluir os usuários). Essas funções têm identificadores conhecidos, assim, você pode substituir o parâmetro `-RoleMemberObjectId` pela `ObjectId` acima e executar os comandos a seguir. Para ver a lista de todas as funções de diretório, tente executar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Agora, você tem um aplicativo que tem permissão para criar, ler, atualizar e excluir usuários do locatário B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Baixar, configurar e compilar o código de exemplo
Primeiro, baixe o código de exemplo e execute-o. Em seguida, faremos uma análise mais detalhada.  Você pode [baixar o código de exemplo como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Você também pode cloná-lo em um diretório de sua escolha:

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

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Agora, clique com o botão direito do mouse na solução `B2CGraphClient` e compile novamente o exemplo. Se tiver êxito, agora deverá você ter um arquivo executável `B2C.exe` localizado em `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Criar operações CRUD do usuário usando a API do Graph
Para usar o B2CGraphClient, abra um prompt de comando `cmd` do Windows e altere o diretório para o diretório `Debug`. Em seguida, execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Isso exibirá uma breve descrição de cada comando. Sempre que você invoca um desses comandos, o `B2CGraphClient` faz uma solicitação para a API do Graph do Azure AD.

### <a name="get-an-access-token"></a>Obter um token de acesso
Qualquer solicitação para a API do Graph requer um token de acesso para autenticação. `B2CGraphClient` usa a ADAL (Biblioteca de Autenticação do Active Directory) de software livre para ajudar a obter tokens de acesso. A ADAL facilita a aquisição de tokens, fornecendo uma API simples e cuidando de alguns detalhes importantes, como armazenar em cache os tokens de acesso. No entanto, você não precisa usar a ADAL para obter tokens. Você também pode obter tokens ao criar solicitações HTTP.

> [!NOTE]
> Este exemplo de código usa o ADAL v2 para se comunicar com a API do Graph.  Você deve usar o ADAL v2 ou v3 para obter tokens de acesso que podem ser usados com a API do Graph do Azure AD.
> 
> 

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

Você pode obter um token de acesso para a API do Graph chamando o método ADAL `AuthenticationContext.AcquireToken(...)` . Em seguida, a ADAL retorna um `access_token` que representa a identidade do aplicativo.

### <a name="read-users"></a>Ler usuários
Quando quiser obter uma lista de usuários ou obter um usuário específico da API do Graph, você poderá enviar uma solicitação HTTP `GET` para o ponto de extremidade `/users`. Uma solicitação para todos os usuários em um locatário tem esta aparência:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```
 > B2C Get-User
 ```

Há dois aspectos importantes a serem observados:

* O token de acesso adquirido pela ADAL foi adicionado ao cabeçalho `Authorization` usando o esquema `Bearer`.
* Para locatários B2C, você deve usar o parâmetro de consulta `api-version=1.6`.

Ambos esses detalhes são tratados no `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
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

### <a name="create-consumer-user-accounts"></a>Criar contas de usuário consumidor
Ao criar contas de usuário no locatário B2C, você pode enviar uma solicitação HTTP `POST` para o ponto de extremidade `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A maioria das propriedades nesta solicitação é necessária para criar usuários consumidores. Para saber mais, clique [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Observe que os comentários `//` foram incluídos para fins de ilustração. Não os inclua em uma solicitação real.

Para ver a solicitação, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O comando `Create-User` usa um arquivo .json como parâmetro de entrada. Ele contém uma representação JSON de um objeto de usuário. Existem dois arquivos .json de exemplo no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos necessários acima, há vários campos opcionais incluídos nesses arquivos que você pode usar. Detalhes sobre os campos opcionais podem ser encontrados na [Referência de entidade da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Você pode ver como a solicitação POST é construída em `B2CGraphClient.SendGraphPostRequest(...)`.

* Ela anexa um token de acesso ao cabeçalho `Authorization` da solicitação.
* Ela define `api-version=1.6`.
* Ela inclui o objeto de usuário JSON no corpo da solicitação.

> [!NOTE]
> Se as contas que deseja migrar de um repositório do usuário existente tiverem um nível de segurança de senha menor do que o [nível de segurança de senha forte imposto pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), você poderá desabilitar o requisito de senha forte usando o valor `DisableStrongPassword` na propriedade `passwordPolicies`. Por exemplo, você pode modificar a solicitação de criação de usuário fornecida acima da seguinte maneira: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Atualizar contas de usuário consumidor
Quando você atualiza os objetos de usuário, o processo é semelhante àquele usado para criar objetos de usuário. Mas esse processo usa o método HTTP `PATCH` :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Tente atualizar um usuário atualizando os arquivos JSON com novos dados. Você pode usar o `B2CGraphClient` para executar um destes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

### <a name="search-users"></a>Pesquisar usuários
É possível pesquisar usuários em seu locatário B2C de duas maneiras. Uma delas é usar a ID de objeto do usuário. A segunda opção é usar o identificador de entrada do usuário (ou seja, a propriedade `signInNames`).

Execute um dos seguintes comandos para pesquisar um usuário específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Aqui estão alguns exemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Excluir usuários
O processo de exclusão de um usuário é simples. Use o método HTTP `DELETE` e construa a URL com a ID de objeto correta:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, insira esse comando e exiba a solicitação Delete exclusão resultante que é mostrada no console:

```
> B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar esta solicitação.

Você pode executar outras ações com a API do Graph do Azure AD além do gerenciamento de usuários. A [Referência da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece detalhes de cada ação, bem como solicitações de exemplo.

## <a name="use-custom-attributes"></a>Usar atributos personalizados
Quase todos os aplicativos consumidores precisam armazenar algum tipo de informação de perfil de usuário personalizada. Uma maneira de fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma maneira que trata qualquer outra propriedade em um objeto de usuário. Você pode atualizar, excluir ou consultar o atributo, enviá-lo como uma declaração de entrada em tokens e assim por diante.

Para definir um atributo personalizado no locatário B2C, veja a [Referência de atributo personalizado do B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos no locatário B2C usando `B2CGraphClient`:

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

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade nos objetos de usuário.  Atualize seu arquivo .json com a nova propriedade e um valor para a propriedade e execute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Usando `B2CGraphClient`, você terá um aplicativo de serviço que pode gerenciar os usuários de locatário B2C de forma programática. `B2CGraphClient` usa sua própria identidade de aplicativo para se autenticar na API do Graph do Azure AD. Ele também adquire tokens usando um segredo do cliente. Ao incorporar essa funcionalidade a seu aplicativo, lembre-se de alguns pontos importantes para aplicativos B2C:

* Você precisa conceder ao aplicativo as permissões apropriadas no locatário.
* Por enquanto, você precisa usar a versão 2 da ADAL para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
* Ao chamar a API do Graph, use `api-version=1.6`.
* Quando você cria e atualiza usuários consumidores, algumas propriedades são obrigatórias, conforme descrito acima.

Se você tiver perguntas ou solicitações de ações que deseja executar usando a API do Graph em seu locatário B2C, deixe um comentário sobre este artigo ou apresente um problema no repositório de código de exemplo do GitHub.


