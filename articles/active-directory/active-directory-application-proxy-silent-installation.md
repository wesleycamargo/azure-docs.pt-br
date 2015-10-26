<properties
	pageTitle="Como instalar silenciosamente o Conector de proxy de aplicativo do Azure AD"
	description="Aborda como realizar a instalação silenciosa do Conector de proxy de Aplicativo do Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="rkarlin"/>

# Como instalar silenciosamente o Conector de proxy de aplicativo do Azure AD

Você pode querer enviar um script de instalação para vários servidores ou para servidores do Windows que não têm interface do usuário habilitada. Este tópico explica como criar um script do Windows PowerShell que permite a instalação autônoma e instala e registra o Conector de proxy de aplicativo do Azure AD.

## Como habilitar o acesso
O Proxy de Aplicativo funciona por meio da instalação de um serviço leve do Windows Server chamado Conector dentro de sua rede. Para o Conector do proxy de aplicativo funcionar, ele deve ser registrado com o diretório do Azure AD usando uma senha e um administrador global. Normalmente, isso é inserido durante a instalação do Conector em uma caixa de diálogo pop-up. Você também pode usar o Windows PowerShell para criar um objeto de credencial e usar o token criado para inserir suas informações de registro, ou pode criar seu próprio token a ser usado para inserir suas informações de registro.

## Etapa 1: Instalar o Conector sem registro


Instale o MSIs do Conector sem registrar o Conector, da seguinte maneira:


1. Abra um prompt de comando.
2. Execute o seguinte comando no qual /q significa instalação silenciosa - a instalação não solicitará que você aceite o contrato de licença de usuário final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Etapa 2: Registre o Conector com o Active Directory do Azure
Isso pode ser feito usando um dos métodos a seguir: - Registrar o Conector usando um objeto de credencial do Windows PowerShell – Registrar o Conector usando um token criado offline

### Registrar o Conector usando um objeto de credencial do Windows PowerShell


1. Crie o objeto de credencial do Windows PowerShell executando o seguinte, em que <username> e <password> devem ser substituídos pelo nome de usuário e senha para seu diretório:

        $User = "<username>" 
        $PlainPassword = '<password>' 
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force 
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword 
    
2. 	Vá para **C:Arquivos de ProgramasAAD App Proxy Connector** e execute o arquivo **Register Connector.PS1** no Windows PowerShell.
3. Use o objeto de credenciais do PowerShell criado para inserir seu de nome de usuário e senha de registro do Conector no script executando o seguinte, em que $cred é o nome do objeto de credenciais do PowerShell criado:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred 


### Registre o Conector usando um token criado offline

1. Crie um token offline usando a classe AuthenticationContext, por exemplo:

        #region constants /// /// The AAD authentication endpoint uri /// static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");
        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
		static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");
		
		/// <summary>
		/// The AppIdUri of the registration service in AAD
		/// </summary>
		static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

		#endregion


		public static void GetAuthenticationToken()
		{
    		AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);
	    AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);


	        if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
    	    {
          Trace.TraceError("Authentication result, token or tenant id returned are null");
          throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
    	}

    	string token = authResult.AccessToken;
    	string tenantID = authResult.TenantId;
		}

2. Quando tiver o token, crie uma SecureString usando o token: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Execute o seguinte comando do Windows PowerShell, em que SecureToken é o nome do token criado anteriormente: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## O que vem a seguir?
Você pode fazer muito mais com o Proxy de Aplicativo:


- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)


### Saiba mais sobre o Proxy de Aplicativo
- [Veja nossa ajuda online](active-directory-application-proxy-enable.md)
- [Confira o blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
- [Assista aos nossos vídeos no Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionais
* [Inscrever-se no Azure como uma organização](../sign-up-organization.md)
* [Identidade do Azure](../fundamentals-identity.md)

<!---HONumber=Oct15_HO3-->