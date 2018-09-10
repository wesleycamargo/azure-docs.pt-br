---
title: Autenticar com o Azure Active Directory para acessar dados de blob e fila de dados dos seus aplicativos (visualização) | Microsoft Docs
description: Use o Azure Active Directory para autenticar a partir de um aplicativo e, em seguida, autorizar solicitações de recursos de Armazenamento do Microsoft Azure (visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: d065dd6db361c5c348713c6e1ceabe3a4c42c312
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577697"
---
# <a name="authenticate-with-azure-active-directory-from-an-azure-storage-application-preview"></a>Autenticar com Microsoft Azure Active Directory a partir de um aplicativo do Armazenamento do Microsoft Azure (versão prévia)

Uma das principais vantagens de usar o Azure AD (Azure Active Directory) com o Armazenamento do Microsoft Azure é que as credenciais não precisam mais ser armazenadas no código. Em vez disso, é possível solicitar um token de acesso OAuth 2.0 do Azure AD. O Azure AD trata a autenticação da entidade de segurança (um usuário, grupo ou entidade de serviço) executando o aplicativo. Se a autenticação tiver êxito, o Azure AD retornará o token de acesso ao aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações ao Armazenamento do Microsoft Azure.

Este artigo mostra como configurar o aplicativo para autenticação com o Azure AD. O exemplo de código apresenta o .NET, mas outras linguagens usam uma abordagem semelhante.

Antes de poder autenticar uma entidade de segurança a partir do aplicativo do Armazenamento do Microsoft Azure, defina as configurações de RBAC (controle de acesso baseado em função) para essa entidade de segurança. O Armazenamento do Microsoft Azure define funções RBAC que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança recebe acesso para esse recurso. Para obter mais informações, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

> [!IMPORTANT]
> Esta versão prévia é destinada apenas para uso não produtivo. Os SLAs (contratos de nível de serviço) de produção não estarão disponíveis até que a integração do Microsoft Azure Active Directory para Armazenamento do Microsoft Azure seja declarada geralmente disponível. Se a integração do Microsoft Azure Active Directory ainda não tiver suporte no seu cenário, continue usando os tokens de autorização ou a chave compartilhada nos aplicativos. Para obter informações adicionais sobre a versão prévia, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md).
>
> Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD

A primeira etapa do uso do Azure AD para autorizar o acesso a recursos de armazenamento é registrando o aplicativo cliente em um locatário do Azure AD. Registrar o aplicativo permite chamar a [ADAL (Biblioteca de Autenticação do Active Directory)](../../active-directory/active-directory-authentication-libraries.md) (ADAL) do seu código. A ADAL fornece uma API para realizar a autenticação no Azure AD por meio do aplicativo. Registrar o aplicativo também permite que você autorize chamadas desse aplicativo para as APIs do Armazenamento do Microsoft Azure com um token de acesso.

Ao registrar o aplicativo, você fornece informações sobre ele ao Azure AD. O Azure AD, em seguida, fornece uma ID do cliente (também chamado de *ID do aplicativo*) que você usa para associar o aplicativo ao Azure AD no tempo de execução. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Para registrar o aplicativo do Armazenamento do Microsoft Azure, siga as etapas na seção [Adicionar um aplicativo](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md#adding-an-application) em [Integração de aplicativos com o Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Se você registrar o aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para **URI de Redirecionamento**. O valor não precisa ser um ponto de extremidade real.

![Captura de tela mostrando como registrar o aplicativo de armazenamento com Azure AD](./media/storage-auth-aad-app/app-registration.png)

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

    ![Captura de tela mostrando permissões para armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Em **Selecionar permissões**, marque a caixa ao lado de **Acessar Armazenamento do Microsoft Azure** e clique em **Selecionar**.
7. Clique em **Concluído**.

As janelas de **Permissões Necessárias** agora mostram que o aplicativo do Azure AD tem acesso ao Active Directory do Azure e ao Armazenamento do Microsoft Azure. As permissões são concedidas ao Azure AD automaticamente quando você registra o aplicativo no Azure AD pela primeira vez.

![Captura de tela mostrando permissões de aplicativo de registro](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: criar um blob de blocos

O exemplo de código mostra como obter um token de acesso do Azure AD. O token de acesso é usado para autenticar o usuário especificado e, em seguida, autoriza uma solicitação para criar um blob de blocos. Para que esse exemplo funcione, primeiro siga as etapas descritas nas seções anteriores.

> [!NOTE]
> Como proprietário da conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados. Você deve atribuir explicitamente a si mesmo uma função RBAC para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila. 
>
> Por exemplo, para executar o código de exemplo em uma conta de armazenamento em que você é um proprietário e sob sua própria identidade de usuário, você deverá atribuir a função RBAC para Colaborador de Dados do Blob a si mesmo. Caso contrário, a chamada para criar o blob falhará com o código de status HTTP 403 (Proibido). Para obter mais informações, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidos para autenticação com Azure AD

Para autenticar uma entidade de segurança com Azure AD, você precisa incluir alguns valores conhecidos no código.

#### <a name="azure-ad-oauth-endpoint"></a>Ponto de extremidade do OAuth do Azure AD

O ponto de extremidade de autoridade do Azure AD básico para OAuth 2.0 é o seguinte, em que *tenant-id* é a ID do locatário do Active Directory (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

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

### <a name="add-references-and-using-statements"></a>Adicione referências e usando instruções  

No Visual Studio, instale a versão prévia da biblioteca de clientes do Armazenamento do Microsoft Azure. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Digite o seguinte comando no console para instalar a versão mais recente da biblioteca de cliente para .NET:

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
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
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

Por fim, use o token de acesso para criar novas credenciais de armazenamento e use essas credenciais para criar o blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> A integração do Microsoft Azure Active Directory ao Armazenamento do Microsoft Azure requer o uso de HTTPS para operações de Armazenamento do Microsoft Azure.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para armazenamento do Azure, consulte [Gerenciar os direitos de acesso aos dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).
- Para saber como usar a Identidade de Serviço Gerenciada com o Armazenamento do Microsoft Azure, consulte [Autenticar com Azure AD a partir de uma Identidade de Serviço Gerenciada do Azure (versão prévia)](storage-auth-aad-msi.md).
- Para saber como efetuar logon na CLI do Azure e no PowerShell com uma identidade do Azure AD, consulte [Use uma identidade do Azure AD para acessar o Armazenamento do Microsoft Azure com a CLI ou o PowerShell (versão prévia)](storage-auth-aad-script.md).
- Para obter informações adicionais sobre a integração do Azure AD para Filas e Blobs do Azure, consulte a postagem no blog da equipe do Armazenamento do Microsoft Azure, [Anunciando a versão prévia de autenticação do Azure AD para Armazenamento do Microsoft Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



