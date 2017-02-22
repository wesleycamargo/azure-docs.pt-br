---
title: "Instalação silenciosa do Conector de Proxy do Aplicativo do Azure AD | Microsoft Docs"
description: "Aborda como realizar a instalação autônoma do Conector de roxy de Aplicativo do Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: cf00d47efc613f7bdc152c1b5f0d0830fb44a785


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Como instalar silenciosamente o Conector de proxy de aplicativo do Azure AD
Você deseja conseguir enviar um script de instalação para vários servidores do Windows ou para os servidores do Windows que não têm uma interface de usuário ativada. Este tópico explica como criar um script do Windows PowerShell que permite a instalação autônoma e instala e registra o Conector de Proxy de Aplicativo do Azure AD.

Esse recurso é útil quando você deseja:

* Instale o conector em máquinas que não possuam camada de interface do usuário ou quando você não puder realizar o RDP na máquina.
* Instalar e registrar vários conectores ao mesmo tempo.
* Integrar a instalação e o registro do conector como parte de outro procedimento.
* Criar uma imagem de servidor padrão que contém bits do conector, mas que não está registrada.

## <a name="enabling-access"></a>Como habilitar o acesso
O Proxy de Aplicativo funciona por meio da instalação de um serviço leve do Windows Server chamado Conector dentro de sua rede. Para o Conector do proxy de aplicativo funcionar, ele deve ser registrado com o diretório do Azure AD usando uma senha e um administrador global. Normalmente, essa informação é inserida durante a instalação do Conector em uma caixa de diálogo pop-up. Você pode, ainda, usar o Windows PowerShell para criar um objeto de credencial para inserir suas informações de registro ou pode criar seu próprio token e usá-lo para inserir suas informações de registro.

## <a name="step-1--install-the-connector-without-registration"></a>Etapa 1: Instalar o Conector sem registro
Instale o MSIs do Conector sem registrar o Conector, da seguinte maneira:

1. Abra um prompt de comando.
2. Execute o seguinte comando no qual /q significa instalação silenciosa - a instalação não solicitará que você aceite o Contrato de Licença de Usuário Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Etapa 2: Registre o Conector com o Active Directory do Azure
Isso pode ser feito usando um dos métodos a seguir:

* Registrar o Conector usando um objeto de credencial do Windows PowerShell
* Registre o Conector usando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrar o Conector usando um objeto de credencial do Windows PowerShell
1. Crie o objeto Credenciais do Windows PowerShell executando o seguinte, onde \<nome de usuário\> e \<senha\> devem ser substituídos pelo nome de usuário e senha de seu diretório:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vá para **C:\Arquivos de Programa\Microsoft AAD App Proxy Connector** e execute o script usando o objeto de credenciais do PowerShell criado, onde $cred é o nome do objeto de credenciais do PowerShell:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registre o Conector usando um token criado offline
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


2. Quando tiver o token, crie uma SecureString usando o token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Execute o seguinte comando do Windows PowerShell, substituindo \<GUID de locatário\> com sua ID de diretório:

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>Próximas etapas 
* [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


