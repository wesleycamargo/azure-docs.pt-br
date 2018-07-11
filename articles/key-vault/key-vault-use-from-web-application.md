---
title: Usar o tutorial do Azure Key Vault a partir de um aplicativo Web | Microsoft Docs
description: Use este tutorial para ajudá-lo a saber como usar o Azure Key Vault por meio de um aplicativo Web.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: adhurwit
ms.openlocfilehash: 5cd764395e91a82973318da7284b28d7a43d35ea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115046"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Tutorial: Usar o Azure Key Vault em um aplicativo Web
Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web no Azure. Ele mostra o processo de acessar um segredo de um Azure Key Vault para uso em um aplicativo Web. Em seguida, o tutorial baseia-se nesse processo e usa um certificado em vez de um segredo do cliente. Esse tutorial foi projetado para desenvolvedores da Web que compreendem os fundamentos da criação de aplicativos Web no Azure. 

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Adicionar configurações do aplicativo ao arquivo web.config
> * Adicionar método para obter um token de acesso
> * Recuperar o token ao iniciar o aplicativo
> * Autenticar com um certificado 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes itens:

* Um URI para um segredo em um Azure Key Vault
* Uma ID do Cliente e um Segredo do Cliente para um aplicativo Web registrado no Azure Active Directory que tenha acesso ao seu Key Vault
* Um aplicativo Web. Este tutorial mostra as etapas para um aplicativo ASP.NET MVC implantado no Azure como um aplicativo Web.

Conclua as etapas em [Introdução ao Azure Key Vault](key-vault-get-started.md) para obter o URI para um segredo, ID de cliente, segredo do cliente e registrar o aplicativo. O aplicativo Web acessará o cofre e precisa ser registrado no Azure Active Directory. Ele também precisa ter direitos de acesso ao Key Vault. Caso contrário, volte a Registrar um Aplicativo no tutorial de Introdução e repita as etapas listadas. Para obter mais informações sobre a criação de aplicativos Web do Azure, consulte [Visão geral de aplicativos Web](../app-service/app-service-web-overview.md).

Este exemplo depende de provisionamento manual das identidades do Azure Active Directory. Atualmente, há um novo recurso em versão prévia chamado [Identidade de Serviço Gerenciada (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), que pode provisionar automaticamente as identidades do Azure Active Directory. Para obter mais informações, consulte o exemplo na [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) e o tutorial [MSI com o tutorial do Serviço de Aplicativo e Funções](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 


## <a id="packages"></a>Adicionar pacotes NuGet

Há dois pacotes que precisam estar instalados para o seu aplicativo Web.

* Biblioteca de autenticação do Active Directory - possui métodos para interagir com o Active Directory do Azure e gerenciar a identidade do usuário
* Biblioteca do Azure Key Vault - possui métodos para interagir com o Azure Key Vault

Ambos os pacotes podem ser instalados usando o console do Gerenciador de Pacotes e o comando Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificar o web.config

Há três configurações de aplicativo que precisam ser adicionadas ao arquivo web.config da forma a seguir. Adicionaremos os valores reais no portal do Azure para um nível adicional de segurança.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Adicionar método para obter um token de acesso

Para usar a API do Key Vault é necessário um token de acesso. O cliente do Key Vault lida com chamadas para a API de Key Vault, mas você precisa fornecer uma função que obtém o token de acesso. O exemplo a seguir é um código para obter um token de acesso do Azure Active Directory. Esse código pode ir em qualquer lugar do aplicativo. Gosto de adicionar uma classe Utils ou EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Recuperar o segredo ao iniciar o aplicativo

Agora precisamos de código para chamar a API do Key Vault e recuperar o segredo. O código a seguir pode ser colocado em qualquer lugar, desde que seja chamado antes de ser usado. Coloquei esse código no evento Iniciar Aplicativo no Global.asax para que ele seja executado uma vez na inicialização e disponibilize o segredo para o aplicativo.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Adicionar configurações de aplicativo no Portal do Azure

No Aplicativo Web do Azure, agora você pode adicionar valores reais ao AppSettings no portal do Azure. Ao realizar essa etapa, os valores reais não estarão no web.config, mas protegidos pelo Portal onde você tem recursos de controle de acesso separados. Esses valores serão substituídos pelos valores inseridos no web.config. Certifique-se de que os nomes sejam os mesmos.

![Configurações do aplicativo exibidas no portal do Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar com um Certificado em vez de um segredo do cliente

Agora que você sabe o que é autenticar um aplicativo do Azure AD usando a ID do Cliente e o Segredo do Cliente, vamos usar uma ID de Cliente e um certificado. Para usar um certificado em um aplicativo Web do Azure, siga as seguintes etapas:

1. Obtenha ou crie um certificado
2. Associe o certificado a um aplicativo do Azure AD
3. Adicione o código ao seu aplicativo Web para usar o certificado
4. Adicione um certificado ao seu aplicativo Web

### <a name="get-or-create-a-certificate"></a>Obtenha ou crie um certificado

 Faremos um certificado de teste para este tutorial. A seguir está um script para criar um certificado autoassinado. Altere o diretório em que você deseja que os arquivos de certificado sejam criados.  Para a data inicial e a data final do certificado, use a data atual mais 1 ano.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

Anote a data de término e a senha para o .pfx (neste exemplo: 15 de maio de 2019 e MinhaSenha). Você precisará delas para o script a seguir. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associe o certificado a um aplicativo do Azure AD

Agora que você tem um certificado, você precisará associá-lo a um aplicativo do Azure AD. A associação pode ser concluída por meio do PowerShell. Execute os seguintes comandos para associar o certificado ao aplicativo do Azure AD:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Depois de executar esses comandos, você verá o aplicativo no Azure AD. Ao pesquisar os registros do aplicativo, verifique se você selecionou **Meus aplicativos** em vez de “Todos os aplicativos” na caixa de diálogo de pesquisa. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Adicione o código ao seu aplicativo Web para usar o certificado

Agora, adicionaremos o código ao seu Aplicativo Web para acessar o certificado e usá-lo para autenticação. 

Primeiro, há um código para acessar o certificado. Observe que StoreLocation é CurrentUser em vez de LocalMachine. E estamos fornecendo “false” para o método Find porque estamos usando um certificado de teste.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Em seguida, há o código que usa CertificateHelper e cria um ClientAssertionCertificate necessário para autenticação.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Este é o novo código para obter o token de acesso. Esse código substitui o método GetToken no exemplo anterior. Eu dei a ele um nome diferente por conveniência. Coloquei todo esse código na classe Utils do projeto do Aplicativo Web para facilidade de uso.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



A última alteração de código é feita no método Application_Start. Primeiro, precisamos chamar o método GetCert() para carregar o ClientAssertionCertificate. E então alteramos o método de retorno de chamada que fornecemos ao criarmos um novo KeyVaultClient. Esse código substitui o código que tínhamos no exemplo anterior.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Adicionar um certificado ao seu aplicativo web por meio do Portal do Azure

Adicionar um certificado ao seu Aplicativo Web é um processo simples de duas etapas. Primeiro, vá até o portal do Azure e navegue até o seu Aplicativo Web. Em Configurações para o seu Aplicativo Web, clique na entrada para **Configurações de SSL**. Quando ele for aberto, carregue o certificado que você criou no exemplo anterior, KVWebApp.pfx. Certifique-se de que você se lembra da senha do pfx.

![Adicionar um certificado a um Aplicativo Web no portal do Azure][2]

A última coisa que você precisa fazer é adicionar uma Configuração de Aplicativo ao Aplicativo Web com o nome WEBSITE\_LOAD\_CERTIFICATES e um valor *. Essa etapa garantirá que todos os Certificados são carregados. Se quiser carregar apenas os Certificados carregados, você poderá inserir uma lista separada por vírgulas das impressões digitais deles.


## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o serviço de aplicativo, o Key Vault e o aplicativo do Azure AD que você usou para o tutorial.  


## <a id="next"></a>Próximas etapas
> [!div class="nextstepaction"]
>[Referência da API de Gerenciamento do Azure Key Vault](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 