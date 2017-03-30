---
title: Usar o Azure Active Directory para autenticar do Lote do Azure | Microsoft Docs
description: "O Lote dá suporte ao Azure AD para a autenticação do serviço Lote e o provedor de recursos do Lote."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Autenticar das soluções do Lote com o Active Directory

O Lote do Azure oferece suporte à autenticação com [Azure Active Directory][aad_about] (Azure AD) para o serviço Lote e o serviço de Gerenciamento do Lote. O Azure AD é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. O Azure em si usa o AD do Azure (Active Directory do Azure) para a autenticação de seus clientes, administradores de serviços e usuários organizacionais.

Neste artigo, exploraremos usando o Azure AD para autenticação de aplicativos que usam a biblioteca do lote gerenciamento .NET ou a biblioteca .NET do Lote. No contexto das APIs do .NET no Lote, vamos mostrar como usar o Azure AD para autenticar um administrador ou coadministrador de assinatura usando a autenticação integrada. O usuário autenticado pode então emitir solicitações para o Lote do Azure.

Também é possível usar o Azure AD para autenticar o acesso a um aplicativo executado em modo autônomo. Agora vamos enfocar usando a autenticação integrada do Azure AD e consultar outros recursos para saber mais sobre a autenticação de aplicativos autônomos.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Usar o Azure AD com soluções de Gerenciamento do Lote

A biblioteca .NET do Gerenciamento do Lote expõe os tipos para trabalhar com contas, chaves de conta, aplicativos e pacotes de aplicativos do Lote. A biblioteca .NET do Gerenciamento do Lote é um cliente do provedor de recursos do Azure e é usada junto com o [Azure Resource Manager][resman_overview] para gerenciar esses recursos por meio de programação. 

O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente de provedor de recursos do Azure, incluindo a biblioteca .NET do Gerenciamento do Lote e por meio do [Azure Resource Manager][resman_overview].

Nesta seção, usamos o projeto de exemplo [AccountManagment][acct_mgmt_sample], disponível no GitHub, para percorrer usando o Azure AD com a biblioteca .NET do Gerenciamento do Lote. O exemplo AccountManagement é um aplicativo de console que acessa uma assinatura por meio de programação, cria um grupo de recursos e uma nova conta do Lote e executa algumas operações na conta. 

Para saber mais sobre como usar a biblioteca .NET do Gerenciamento do Lote e o exemplo AccountManagement, veja [Gerenciar contas e cotas do Lote com a biblioteca de cliente do Gerenciamento do Lote para .NET](batch-management-dotnet.md).

### <a name="register-your-application-with-azure-ad"></a>Registrar seu aplicativo no Azure AD

A ADAL [Biblioteca de Autenticação do Active Directory][aad_adal] fornece uma interface programática para o Azure AD para uso em seus aplicativos. Para chamar a ADAL de seu aplicativo, você deve registrar seu aplicativo em um locatário do Azure AD. Quando você registra seu aplicativo, fornece ao Azure AD informações sobre seu aplicativo, incluindo um nome para ele dentro do locatário do Azure AD. Em seguida, o Azure AD fornece uma ID de aplicativo que você usa para associar seu aplicativo ao Azure AD em tempo de execução. Para saber mais sobre a ID do aplicativo, veja [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Para registrar o aplicativo de exemplo AccountManagement, siga as etapas da seção [Adição de um aplicativo](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) em [Integração de aplicativos ao Azure Active Directory][aad_integrate]. Especifique **aplicativo cliente nativo** para o tipo de aplicativo. Para o **URI de redirecionamento**, você pode especificar qualquer URI válido (como `http://myaccountmanagementsample`), pois ele não precisa ser um ponto de extremidade real:

![](./media/batch-aad-auth/app-registration-management-plane.png)

Depois de concluir o processo de registro, você verá a ID do aplicativo e a ID de objeto (entidade de serviço) listados para seu aplicativo.  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>Atualizar seu código para fazer referência a ID do aplicativo 

O aplicativo cliente usa a ID do aplicativo (também conhecida como a ID do cliente) para acessar o Azure AD em tempo de execução. Depois de registrar seu aplicativo no portal do Azure, atualize seu código para usar a ID do aplicativo fornecida pelo Azure AD para seu aplicativo registrado. No aplicativo de exemplo de AccountManagement, copie a ID do aplicativo do portal do Azure à constante apropriada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Também copie o URI que você especificou durante o processo de registro de redirecionamento.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceder à API do Azure Resource Manager acesso a seu aplicativo

Em seguida, você precisará delegar o acesso ao seu aplicativo para a API do Azure Resource Manager. O identificador do Azure AD para a API do Resource Manager é **API de Gerenciamento de Serviços do Microsoft Azure**.

Siga estas etapas no portal do Azure:

1. No painel de navegação à esquerda do portal do Azure, escolha **Mais Serviços**, clique em **Registros de Aplicativo** e clique em **Adicionar**.
2. Pesquise pelo nome do seu aplicativo na lista de registros do aplicativo:

    ![Procure o nome do aplicativo](./media/batch-aad-auth/search-app-registration.png)

3. Exibição de **configurações** folha. Na seção **Acesso à API**, selecione **Permissões necessárias**.
4. Clique em **adicionar** para adicionar uma nova permissão necessária. 
5. Na etapa 1, digite **API de gerenciamento de serviços do Microsoft Azure**, selecione essa API da lista de resultados e clique no **selecione** botão.
6. Na etapa 2, selecione a caixa de seleção Avançar ao **Aceder a modelo de implantação clássico do Azure enquanto usuários da organização**e clique no **selecione** botão.
7. Clique no botão **Concluído**.

O **permissões necessárias** folha agora mostra que as permissões para o seu aplicativo são concedidas ao ADAL e APIs do Gerenciador de recursos. Permissões são concedidas a ADAL por padrão, quando você primeiro registra seu aplicativo com o Azure AD.

![Delegar permissões para a API do Azure Resource Manager](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

O aplicativo de exemplo AccountManagement define constantes que fornecem o ponto de extremidade do Azure AD e do Azure Resource Manager. O aplicativo de exemplo usa constantes para consultar o Azure AD para obter informações de assinatura. Deixe essas constantes inalterados:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

Depois de registrar o exemplo de AccountManagement no locatário do Azure AD e de fornecer os valores necessários no código-fonte, o exemplo está pronto para se autenticar usando o Azure AD. Quando você executar o exemplo, o ADAL tenta adquirir um token de autenticação. Nesta etapa, ele solicitará suas credenciais da Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Depois de fornecer suas credenciais, o aplicativo de exemplo poderá emitir solicitações autenticadas para o serviço de Gerenciamento do Lote. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Usar o Azure AD com soluções do serviço Lote

A biblioteca do .NET do Lote fornece tipos para a criação de fluxos de trabalho de processamento paralelo com o serviço Lote. O serviço Lote dá suporte à autenticação por [Chave Compartilhada](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) e à autenticação por meio do Azure AD. Nesta seção, discutiremos a autenticação por meio do Azure AD.

>[!NOTE]
>Quando você cria uma conta de lote, você pode especificar se os pools devem ser alocados em uma assinatura gerenciada pelo lote ou em uma assinatura de usuário. Se sua conta aloca pools em uma assinatura de usuário, você deve usar o AD do Azure para autenticar solicitações para recursos nessa conta.
>
>

A autenticação de aplicativos .NET do Lote por meio do Azure AD é semelhante à autenticação de aplicativos .NET do Gerenciamento do Lote. Há algumas diferenças, descritas nesta seção.

### <a name="batch-service-endpoints"></a>Pontos de extremidade do serviço Lote

Os pontos de extremidade do serviço Lote diferem daqueles que você usa com o .NET do Gerenciamento do Lote.

O ponto de extremidade do Azure AD para o serviço Lote é:

`https://login.microsoftonline.com/common`

O ponto de extremidade de recursos para o serviço Lote é:

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>Conceder o acesso de API do serviço Lote para o seu aplicativo

Antes que você possa autenticar por meio do Azure AD do seu aplicativo Lote, será necessário registrar seu aplicativo no Azure AD e conceder acesso à API de serviço Lote. O identificador do Azure AD para a API do serviço Lote é **Microsoft Azure Batch (MicrosoftAzureBatch)**.

1. Para registrar o aplicativo Lote, siga as etapas da seção [Adicionar um aplicativo](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) em [Integração de aplicativos com o Azure Active Directory][aad_integrate]. Para o **URI de redirecionamento**, você pode especificar qualquer URI válida. Ele não precisa ser um ponto de extremidade real.

    Depois de registrar seu aplicativo, você verá a ID do aplicativo e a ID de objeto:

    ![Registrar seu aplicativo Lote no Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

2. Em seguida, exibir o **configurações** folha. Na seção **Acesso à API**, selecione **Permissões necessárias**.
3. No **permissões necessárias** folha, clique o **adicionar** botão.
4. Na etapa 1, pesquise por **MicrosoftAzureBatch**, selecione **(MicrosoftAzureBatch) do Microsoft Azure Batch**e clique no **selecione** botão.
5. Na etapa 2, selecione a caixa de seleção Avançar ao **acesso serviço do Azure Batch** e clique no **selecione** botão.
6. Clique no botão **Concluído**.

A folha **Permissões Necessárias** agora mostra que o seu aplicativo do Azure AD concede acesso ao Azure AD e às APIs do Lote do Azure. 

![Permissões de API](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>Autenticação para contas do Lote em uma assinatura do usuário

Quando você cria uma nova conta de lote, você pode escolher a assinatura em que os pools são alocados. Sua escolha afeta como autenticar solicitações feitas a recursos na conta

Por padrão, os pools de lote são alocados em uma assinatura de serviço de lote. Se você escolher essa opção, você pode autenticar solicitações de recursos na conta com a chave compartilhada ou com o Azure AD.

Você também pode especificar que os pools de lote são alocados em uma assinatura de usuário especificado. Se você escolher essa opção, deverá autenticar no Azure AD.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Práticas recomendadas para usar o Azure AD com o Lote

Um token de autenticação do AD do Azure expirará após uma hora. Ao usar uma vida útil longa **BatchClient** de objeto, é recomendável que você recuperar um token da ADAL em cada solicitação para garantir que você sempre tenha um token válido. 

Para fazer isso no .NET, escrever um método que recupera o token do AD do Azure e passar esse método para um **BatchTokenCredentials** objeto como um delegado. O método de representante é chamado em cada solicitação para o serviço de lote para garantir que um token válido seja fornecido. Por padrão ADAL armazena em cache os tokens, para que um novo token é recuperado do AD do Azure somente quando necessário. Para obter um exemplo, veja [Exemplo de código: uso do Azure AD com o .NET do Lote](#code-example-using-azure-ad-with-batch-net) na próxima seção. Para saber mais sobre tokens no AD do Azure, veja [Cenários de autenticação do AD do Azure][aad_auth_scenarios].

### <a name="code-example-using-azure-ad-with-batch-net"></a>Exemplo de código: uso do Azure AD com o .NET do Lote

Para gravar código .NET do Lote que autentique no Azure AD, veja o pacote [.NET do Lote do Azure](https://www.nuget.org/packages/Azure.Batch/) e o pacote [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Inclua as seguintes intruções `using` no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Consulte o ponto de extremidade comum do Azure AD e o ponto de extremidade do Azure AD para o serviço Lote no seu código:  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Consulte o ponto de extremidade da sua conta do Lote:

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no seu registro de aplicativo no portal do Azure; veja a seção intitulada [Conceder o acesso de API do serviço Lote para o seu aplicativo](#grant-the-batch-service-api-access-to-your-application) para recuperá-la. 

```csharp
private const string ClientId = "<application-id>";
```

Também especifique um URI de redirecionamento que pode ser qualquer URI válida.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do AD do Azure. O método **AcquireTokenAsync** solicita ao usuário as credenciais e usa essas credenciais para adquirir um novo token.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que use o delegado como um parâmetro. Use essas credenciais para abrir um objeto **BatchClient**. Você pode usar o objeto **BatchClient** para operações subsequentes no serviço Lote.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

O método de chamada de retorno **GetAuthenticationTokenAsync** mostrado acima usa o Azure AD para autenticação integrada de um usuário que está interagindo com o aplicativo. A chamada para o método **AcquireTokenAsync** solicita ao usuário as credenciais, e o aplicativo continua depois que o usuário as fornece. Você também pode usar o Azure AD para autenticar um aplicativo autônomo usando uma entidade de serviço do Azure AD. Para saber mais, veja [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) e [Usar o portal para criar um aplicativo e uma entidade de serviço do Active Directory que possam acessar recursos](../resource-group-create-service-principal-portal.md).  
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a execução do [aplicativo de exemplo AccountManagement][acct_mgmt_sample], veja [Gerenciar contas e cotas do Lote com a biblioteca de cliente de Gerenciamento do Lote para .NET](batch-management-dotnet.md).

Para saber mais sobre o Azure AD, veja a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos mais detalhados que mostram como usar o ADAL estão disponíveis na biblioteca [Amostras de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).


[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrando aplicativos com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

