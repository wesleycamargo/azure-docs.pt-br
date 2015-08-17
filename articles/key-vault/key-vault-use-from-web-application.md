<properties pageTitle="Use o cofre da chave do Azure em um aplicativo Web | Visão geral" description="Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Usar cofre da chave do Azure em um aplicativo Web #

## Introdução  
Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web no Azure. Ele percorre o processo de acessar um segredo de um cofre de chave do Azure para que possa ser usado no aplicativo Web.

**Tempo estimado para conclusão:** 15 minutos


Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## Pré-requisitos 

Para concluir este tutorial, você precisará do seguinte:

- Um URI para um segredo em um cofre da chave do Azure
- Uma ID de cliente e segredo do cliente para um aplicativo Web registrado no Active Directory do Azure, que tenha acesso ao cofre da chave
- Um aplicativo Web. Mostraremos as etapas para um aplicativo ASP.NET MVC implantado no Azure como um aplicativo Web. 

> [AZURE.NOTE]É essencial que você conclua as etapas listadas em [Introdução ao cofre da chave do Azure](key-vault-get-started.md) para este tutorial, para que você tenha o URI de um segredo e a ID do cliente e o segredo do cliente para um aplicativo Web.

O aplicativo Web que acessará o cofre da chave é aquele que está registrado no Active Directory do Azure e tem acesso ao seu cofre da chave. Se esse não for o caso, volte a Registrar um Aplicativo do tutorial de introdução e repita as etapas listadas.

Esse tutorial foi projetado para desenvolvedores da Web que compreendem os fundamentos da criação de aplicativos Web no Azure. Para obter mais informações sobre aplicativos Web do Azure, consulte [Visão geral de aplicativos Web](../app-service-web-overview.md).



## <a id="packages"></a>Adicionar pacotes NuGet ##
Há três pacotes que precisam estar instalados para o seu aplicativo Web.

- Biblioteca de autenticação do Active Directory - contém métodos para interagir com o Active Directory do Azure e gerenciar a identidade do usuário
- Biblioteca de cofre da chave do Azure - contém métodos para interagir com o cofre da chave do Azure


Esses três esses pacotes podem ser instalados usando o console do Gerenciador de Pacotes e o comando Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Modificar o Web.config ##
Há três configurações de aplicativo que precisam ser adicionadas ao arquivo web.config da forma a seguir.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Se você não for hospedar seu aplicativo como um aplicativo Web do Azure, deve adicionar os valores reais ClientId, Segredo do Cliente e URI de Segredo no web.config. Caso contrário, deixe esses valores fictícios porque vamos adicionar os valores reais no Portal do Azure para um nível adicional de segurança.


## <a id="gettoken"></a>Adicionar método para obter um token de acesso ##
Para usar a API de cofre da chave é necessário um token de acesso. O cliente do cofre da chave lida com chamadas para a API de cofre da chave, mas você precisa fornecer uma função que obtém o token de acesso.

A seguir, o código para obter um token de acesso do Active Directory do Azure. Esse código pode ir em qualquer lugar do aplicativo. Gosto de adicionar uma classe Utils ou EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>Recuperar o segredo ao iniciar o aplicativo ##
Agora precisamos de código para chamar a API do cofre da chave e recuperar o segredo. O código a seguir pode ser colocado em qualquer lugar, desde que seja chamado antes de ser usado. Coloquei esse código no evento Iniciar Aplicativo no Global.asax para que ele seja executado uma vez na inicialização e disponibilize o segredo para o aplicativo.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Adicionar configurações de aplicativo no Portal do Azure (opcional) ##
Se você tiver um aplicativo Web do Azure pode agora adicionar valores reais ao AppSettings no Portal do Azure. Fazendo isso, os valores reais não estarão no web.config, mas protegidos pelo Portal onde você tem recursos de controle de acesso separados. Esses valores serão substituídos pelos valores inseridos no web.config. Certifique-se de que os nomes sejam os mesmos.

![Configurações do aplicativo exibidas no Portal do Azure][1]



## <a id="next"></a>Próximas etapas ##


Para referências de programação, consulte [Referência de API do cliente C# do cofre da chave do Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=August15_HO6-->