---
title: Autenticar com o Azure Active Directory para acessar dados de blob e fila de aplicativos | Microsoft Docs
description: Use o Azure Active Directory para autenticar a partir de um aplicativo e, em seguida, autorizar solicitações para blobs e filas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c4959d6abd1b33c81f731ad0479bd59d271c7797
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483908"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autenticar com o Azure Active Directory de um aplicativo para acesso a blobs e filas

Uma das principais vantagens de usar o Azure AD (Azure Active Directory) com o Armazenamento do Microsoft Azure é que as credenciais não precisam mais ser armazenadas no código. Em vez disso, é possível solicitar um token de acesso OAuth 2.0 do Azure AD. O Azure AD trata a autenticação da entidade de segurança (um usuário, grupo ou entidade de serviço) executando o aplicativo. Se a autenticação tiver êxito, o Azure AD retornará o token de acesso ao aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações ao Armazenamento do Microsoft Azure.

Este artigo mostra como configurar o aplicativo para autenticação com o Azure AD. O exemplo de código apresenta o .NET, mas outras linguagens usam uma abordagem semelhante.

Antes de se autenticar uma entidade de segurança do seu aplicativo de armazenamento do Azure, defina as configurações de RBAC (controle) de acesso baseado em função para essa entidade de segurança. O Armazenamento do Microsoft Azure define funções RBAC que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança recebe acesso para esse recurso. Para obter mais informações, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

Para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Atribuir uma função RBAC a uma entidade de segurança do Azure AD

Para autenticar uma entidade de segurança do seu aplicativo de armazenamento do Azure, primeiro configure as configurações de RBAC (controle) de acesso baseado em função para essa entidade de segurança. O Armazenamento do Microsoft Azure define funções RBAC que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança recebe acesso para esse recurso. Para obter mais informações, consulte [gerenciar direitos de acesso a dados do Azure Blob e fila com RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD

A primeira etapa do uso do Azure AD para autorizar o acesso a recursos de armazenamento é registrando o aplicativo cliente em um locatário do Azure AD. Registrar o aplicativo permite chamar a [ADAL (Biblioteca de Autenticação do Active Directory)](../../active-directory/active-directory-authentication-libraries.md) (ADAL) do seu código. A ADAL fornece uma API para realizar a autenticação no Azure AD por meio do aplicativo. Registrar o aplicativo também permite que você autorize chamadas desse aplicativo para as APIs do Armazenamento do Microsoft Azure com um token de acesso.

Ao registrar o aplicativo, você fornece informações sobre ele ao Azure AD. O Azure AD, em seguida, fornece uma ID do cliente (também chamado de *ID do aplicativo*) que você usa para associar o aplicativo ao Azure AD no tempo de execução. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Para registrar o aplicativo do Armazenamento do Microsoft Azure, siga as etapas na seção [Adicionar um aplicativo](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) em [Integração de aplicativos com o Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Se você registrar o aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para **URI de Redirecionamento**. O valor não precisa ser um ponto de extremidade real.

![Captura de tela mostrando como registrar seu aplicativo de armazenamento com o Azure AD](./media/storage-auth-aad-app/app-registration.png)

Depois de registrar o aplicativo, você verá a ID do aplicativo (ou ID do cliente) em **Configurações** :

![Captura de tela mostrando a ID do cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicativo registradas ao Armazenamento do Microsoft Azure

Em seguida, será necessário conceder permissões de aplicativo para chamar APIs do Armazenamento do Microsoft Azure. Esta etapa permite que o aplicativo autorize chamadas para o Armazenamento do Microsoft Azure com Azure AD.

1. No painel de navegação esquerdo do portal do Azure, escolha **Todos os serviços** e pesquise**Registros de Aplicativo**.
2. Pesquise o nome do aplicativo registrado que você criou na etapa anterior.
3. Selecione o aplicativo registrado e clique em **Configurações**. Na seção **Acesso à API**, selecione **Permissões necessárias**.
4. No **permissões necessárias** folha, clique o **adicionar** botão.
5. Em **Selecionar uma API**, pesquise "Armazenamento do Microsoft Azure" e selecione **Armazenamento do Microsoft Azure** da lista de resultados.

    ![Captura de tela mostrando permissões para o armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Em **Selecionar permissões**, marque a caixa ao lado de **Acessar Armazenamento do Microsoft Azure** e clique em **Selecionar**.
7. Clique em **Concluído**.

As janelas de **Permissões Necessárias** agora mostram que o aplicativo do Azure AD tem acesso ao Active Directory do Azure e ao Armazenamento do Microsoft Azure. As permissões são concedidas ao Azure AD automaticamente quando você registra o aplicativo no Azure AD pela primeira vez.

![Captura de tela mostrando registrar as permissões de aplicativo](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: criar um blob de blocos

O exemplo de código mostra como obter um token de acesso do Azure AD. O token de acesso é usado para autenticar o usuário especificado e, em seguida, autoriza uma solicitação para criar um blob de blocos. Para que esse exemplo funcione, primeiro siga as etapas descritas nas seções anteriores.

> [!NOTE]
> Como proprietário da conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados. Você deve atribuir explicitamente a si mesmo uma função RBAC para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila. 
>
> Por exemplo, para executar o código de exemplo em uma conta de armazenamento em que você é um proprietário e sob sua própria identidade de usuário, você deverá atribuir a função RBAC para Colaborador de Dados do Blob a si mesmo. Caso contrário, a chamada para criar o blob falhará com o código de status HTTP 403 (Proibido). Para obter mais informações, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidos para autenticação com Azure AD

Para autenticar uma entidade de segurança com Azure AD, você precisa incluir alguns valores conhecidos no código.

#### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, a autoridade do Azure AD básica é a seguinte, em que *tenant-id* é a ID do locatário do Active Directory (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

A ID do locatário identifica o locatário do Azure AD a ser usado para autenticação. Para recuperar a ID do locatário, siga as etapas descritas em **Obter a ID do locatário para o Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID do recurso de armazenamento

Use a ID do recurso do Armazenamento do Microsoft Azure para adquirir um token para autenticar solicitações no Armazenamento do Microsoft Azure:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter a ID do locatário para o Azure Active Directory

Para obter a ID de locatário, siga estas etapas:

1. No portal do Azure, selecione seu Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor do GUID fornecido para o **ID de Diretório**. Esse valor também é chamado de ID do locatário.

![Captura de tela mostrando como copiar a ID do locatário](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Digite o seguinte comando no console para instalar a versão mais recente da biblioteca de cliente para .NET:

```
Install-Package WindowsAzure.Storage
```

Além disso, instale a versão mais recente da Biblioteca de Autenticação do Active Directory:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Em seguida, adicione as seguintes instruções no código:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um token OAuth do Azure AD

Em seguida, adicione um método que solicite um token do Azure AD. Para solicitar o token, chame o método [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync). Certifique-se de que você tenha os seguintes valores de etapas que você seguiu anteriormente:

- ID do locatário (diretório)
- ID do cliente (aplicativo)
- URI de redirecionamento de cliente

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Criar o blob de blocos

Por fim, use o token de acesso para criar novas credenciais de armazenamento e usar essas credenciais para criar o blob. Lembre-se de que, para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> A integração do Microsoft Azure Active Directory ao Armazenamento do Microsoft Azure requer o uso de HTTPS para operações de Armazenamento do Microsoft Azure.

No exemplo acima, a biblioteca de cliente .NET lida com a autorização da solicitação para criar o blob de blocos. Outras bibliotecas de cliente de armazenamento também lidar com a autorização da solicitação para você. No entanto, se você estiver chamando uma operação de armazenamento do Azure com um token de OAuth, usando a API REST, você precisará autorizar a solicitação usando o token OAuth.   

Para chamar as operações de serviço Blob e Queue usando tokens de acesso OAuth, passe o token de acesso no cabeçalho **Authorization** usando o esquema **Bearer** e especifique uma versão de serviço de 2017-11-09 ou superior, conforme mostrado no exemplo a seguir:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Para obter mais informações sobre como autorizar as operações de armazenamento do Azure do REST, consulte [autenticar com o Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre como usar identidades gerenciadas de recursos do Azure com o armazenamento do Azure, consulte [autenticar o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).
- Para saber como executar comandos da CLI do Azure e do PowerShell com credenciais do Azure AD, consulte [comandos de execução da CLI do Azure ou o PowerShell com credenciais do Azure AD para acessar dados de blob ou fila](storage-auth-aad-script.md).
