<properties
	pageTitle="Como instalar silenciosamente o Conector de Proxy de Aplicativo do Azure AD | Microsoft Azure"
	description="Aborda como realizar a instalação silenciosa do Conector de proxy de Aplicativo do Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>

# Como instalar silenciosamente o Conector de proxy de aplicativo do Azure AD

Você deseja conseguir enviar um script de instalação para vários servidores do Windows ou para os servidores do Windows que não têm uma interface de usuário ativada. Este tópico explica como criar um script do Windows PowerShell que permite a instalação autônoma e instala e registra o Conector de Proxy de Aplicativo do Azure AD.

## Como habilitar o acesso
O Proxy de Aplicativo funciona por meio da instalação de um serviço leve do Windows Server chamado Conector dentro de sua rede. Para o Conector do proxy de aplicativo funcionar, ele deve ser registrado com o diretório do Azure AD usando uma senha e um administrador global. Normalmente, isso é inserido durante a instalação do Conector em uma caixa de diálogo pop-up. Você pode, ainda, usar o Windows PowerShell para criar um objeto de credencial para inserir suas informações de registro ou pode criar seu próprio token e usá-lo para inserir suas informações de registro.

## Etapa 1: Instalar o Conector sem registro


Instale o MSIs do Conector sem registrar o Conector, da seguinte maneira:


1. Abra um prompt de comando.
2. Execute o seguinte comando no qual /q significa instalação silenciosa - a instalação não solicitará que você aceite o Contrato de Licença de Usuário Final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Etapa 2: Registre o Conector com o Active Directory do Azure
Isso pode ser feito usando um dos métodos a seguir:


- Registrar o Conector usando um objeto de credencial do Windows PowerShell
- Registre o Conector usando um token criado offline

### Registrar o Conector usando um objeto de credencial do Windows PowerShell


1. Crie o objeto de credencial do Windows PowerShell executando o seguinte, em que “<username>” e “<password>” devem ser substituídos pelo nome de usuário e senha para seu diretório:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Vá para **C:\\Arquivos de Programa\\Microsoft AAD App Proxy Connector** e execute o script usando o objeto de credenciais do PowerShell criado: em que $cred é o nome do objeto de credenciais do PowerShell criado:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### Registre o Conector usando um token criado offline

1. Crie um token offline usando a classe AuthenticationContext, com os valores no trecho de código:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

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

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
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

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Quando tiver o token, crie uma SecureString usando o token: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Execute o seguinte comando do Windows PowerShell, onde SecureToken é o nome do token criado acima e tenantID é o GUID do seu locatário: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## Consulte também

- [Habilitar o Proxy de Aplicativo para o Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0622_2016-->