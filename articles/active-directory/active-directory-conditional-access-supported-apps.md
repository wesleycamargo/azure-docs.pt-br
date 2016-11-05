
---
title: Acesso condicional – Quais aplicativos têm suporte | Microsoft Docs
description: Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: markvi

---
# <a name="conditional-access-support-for-applications"></a>Suporte de acesso condicional para aplicativos
As regras de acesso condicional têm suporte em aplicativos conectados do Azure Active Directory, aplicativos SaaS federados pré-integrados, aplicativos que usam senha de logon único e a linha de aplicativos de negócios e o Proxy de aplicativo do Azure AD. Para obter uma lista detalhada dos aplicativos em que você pode habilitar o acesso condicional, veja [Serviços habilitados com acesso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). O acesso condicional funciona com aplicativos móveis e da área de trabalho que usam autenticação moderna. Este tópico explica o que tem suporte em relação à versão móvel e para desktop desses aplicativos.

Aplicativos com autenticação moderna podem exibir a entrada do Azure AD nas páginas. Isso permite que um usuário seja solicitado de forma embutida para autenticação multifator ou mostre uma mensagem voltada para o usuário final quando o acesso é bloqueado. A autenticação moderna também é necessária para que o dispositivo possa autenticar com o Azure AD para que políticas de acesso condicional com base no dispositivo sejam avaliadas.

É importante entender quais aplicativos têm suporte, bem como as etapas que podem ser necessárias para proteger outros pontos de entrada.

## <a name="applications-using-modern-authentication"></a>Aplicativos que usam autenticação moderna
Os aplicativos a seguir oferecem suporte ao acesso condicional ao acessar o Office 365 e outros aplicativos de serviço conectados ao Azure AD:

| Serviço de Destino | Plataforma | Aplicativo |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Aplicativo de Calendário/Email/Pessoas, Outlook 2016, Outlook 2013 (com autenticação moderna habilitada), Skype for Business (com autenticação moderna) |
| Office 365 Exchange Online |Windows 7, Windows 8.1, |Outlook 2016, Outlook 2013 (com autenticação moderna habilitada), Skype for Business (com autenticação moderna) |
| Office 365 Exchange Online |iOS, Android |Aplicativo móvel do Outlook |
| Office 365 Exchange Online |Mac OSX |Outlook 2016 apenas para MFA/local; em breve suporte para políticas com base em dispositivos, suporte para o Skype for Business no futuro |
| Office 365 SharePoint Online |Windows 10 |Aplicativos do Office 2016, Aplicativos universais do Office, Office 2013 (com autenticação moderna habilitada), suporte para aplicativo OneDrive for Business (NGSC ou cliente de sincronização da próxima geração) em breve, suporte de Grupos do Office em breve, suporte para aplicativos do SharePoint em breve |
| Office 365 SharePoint Online |Windows 7, Windows 8.1, |Aplicativos do Office 2016, Office 2013 (com autenticação moderna habilitada), aplicativo OneDrive for Business (cliente de sincronização do Groove) |
| Office 365 SharePoint Online |iOS, Android |Aplicativos móveis do Office |
| Office 365 SharePoint Online |Mac OSX |Aplicativos do Office 2016 somente para MFA/local; suporte para políticas com base em dispositivos em breve |
| Office 365 Yammer |Windows 10, iOS e Android |Aplicativo Office Yammer |
| Dynamics CRM |Windows 10, 7, 8.1, iOS e Android |Aplicativo Dynamics CRM |
| Serviço PowerBI |Windows 10, 7, 8.1, iOS e Android |Aplicativo PowerBI |
| Serviço de Aplicativo Remoto do Azure |Windows 10, 7, 8.1, iOS e Android, Mac OSX |Aplicativo Remoto do Azure |
| Qualquer serviço de aplicativo de Meus Aplicativos |Android e iOS |Qualquer serviço de aplicativo de Meus Aplicativos |

## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicativos que não usam autenticação moderna
Atualmente, os aplicativos que não usam autenticação moderna devem ter acesso bloqueado ao usar outros métodos, pois eles não são impostos por acesso condicional. Isso é basicamente uma consideração para acesso ao Exchange e SharePoint, pois versões anteriores do aplicativo foram compiladas usando protocolos mais antigos.

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online
Protocolos herdados podem ser desabilitados no SharePoint, usando o cmdlet Set-SPOTenant. Esse cmdlet impedirá que os clientes do Office usando os protocolos de autenticação não modernas acessem recursos do SharePoint Online. 

**Exemplo de comando**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

## <a name="office-365-exchange-online"></a>Office 365 Exchange Online
No Exchange, há duas categorias principais de protocolo. Analise e selecione a política certa para a sua organização:

1. Exchange ActiveSync. Por padrão, a política de acesso condicional para o MFA e a Localização não é imposta para o Exchange ActiveSync. Isso permite que o acesso seja protegido por meio da configuração de política do Exchange ActiveSync diretamente ou ao bloquear o Exchange ActiveSync usando regras do AD FS.
2. Protocolos herdados. Protocolos herdados podem ser bloqueados no AD FS. Isso bloqueará o acesso de clientes mais antigos do Office, como o Office 2013 sem autenticação moderna habilitada e versões anteriores.

### <a name="example-ad-fs-rules"></a>Regras do AD FS de exemplo
As regras a seguir podem ser usadas para bloquear o acesso de protocolo herdado no AD FS, em duas configurações comuns.

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>Opção 1: permitir o Exchange ActiveSync e só permitir aplicativos herdados na intranet
Ao aplicar as três regras a seguir ao Objeto de Confiança de Terceira Parte Confiável do AD FS para a Plataforma de Identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync será permitido, juntamente com o navegador e o tráfego de autenticação moderna. Aplicativos herdados serão bloqueados da extranet. 

Regra 1

    `@RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Regra 2

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Regra 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>Opção 2: permitir o Exchange ActiveSync e bloquear aplicativos herdados
Ao aplicar as três regras a seguir ao Objeto de Confiança de Terceira Parte Confiável do AD FS para a Plataforma de Identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync será permitido, juntamente com o navegador e o tráfego de autenticação moderna. Aplicativos herdados serão bloqueados de qualquer localização. 

Regra 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regra 2 

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regra 3 

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");











<!--HONumber=Oct16_HO2-->


