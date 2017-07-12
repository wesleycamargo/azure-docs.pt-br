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
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f4d72d4d11ee64e3431879f6ad1b5d8d091a0c87
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---
<a id="silently-install-the-azure-ad-application-proxy-connector" class="xliff"></a>

# Faça uma instalação silenciosa do Conector de Proxy de Aplicativo do Azure AD
Você deseja conseguir enviar um script de instalação para vários servidores do Windows ou para os servidores do Windows que não têm uma interface de usuário ativada. Este tópico ajuda você a criar um script do Windows PowerShell que permite a instalação autônoma e registro para o Conector de Proxy de Aplicativo do Azure AD.

Esse recurso é útil quando você deseja:

* Instale o conector em máquinas que não possuam camada de interface do usuário ou quando você não puder realizar o RDP na máquina.
* Instalar e registrar vários conectores ao mesmo tempo.
* Integrar a instalação e o registro do conector como parte de outro procedimento.
* Criar uma imagem de servidor padrão que contém bits do conector, mas que não está registrada.

O Proxy de Aplicativo funciona por meio da instalação de um serviço leve do Windows Server chamado Conector dentro de sua rede. Para o Conector do proxy de aplicativo funcionar, ele deve ser registrado com o diretório do Azure AD usando uma senha e um administrador global. Normalmente, essa informação é inserida durante a instalação do Conector em uma caixa de diálogo pop-up. Porém, você ainda pode usar o Windows PowerShell para criar um objeto de credencial para inserir suas informações de registro ou criar seu próprio token e usá-lo para inserir suas informações de registro.

<a id="install-the-connector" class="xliff"></a>

## Instalar o conector
Instale o MSIs do Conector sem registrar o Conector, da seguinte maneira:

1. Abra um prompt de comando.
2. Execute o seguinte comando no qual /q significa instalação silenciosa - a instalação não solicitará que você aceite o Contrato de Licença de Usuário Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

<a id="register-the-connector-with-azure-ad" class="xliff"></a>

## Registrar o conector com o Azure AD
Há dois métodos que você pode usar para registrar o conector:

* Registrar o conector usando um objeto de credencial do Windows PowerShell
* Registrar o conector usando um token criado offline

<a id="register-the-connector-using-a-windows-powershell-credential-object" class="xliff"></a>

### Registrar o conector usando um objeto de credencial do Windows PowerShell
1. Crie o objeto de Credenciais do Windows PowerShell executando o comando a seguir. Substitua *\<nome de usuário\>* e *\<senha\>* pelo nome de usuário e a senha para seu diretório:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vá para **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o script usando o objeto de credenciais do PowerShell criado. Substitua *$cred* pelo nome do objeto de credenciais do PowerShell criado:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

<a id="register-the-connector-using-a-token-created-offline" class="xliff"></a>

### Registrar o conector usando um token criado offline
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
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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

<a id="next-steps" class="xliff"></a>

## Próximas etapas 
* [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)



