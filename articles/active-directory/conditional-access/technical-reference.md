---
title: Referência de configurações de acesso condicional do Active Directory do Azure | Microsoft Docs
description: Obtenha uma visão geral das configurações com suporte em uma política de acesso condicional do Active Directory do Azure.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2019
ms.author: markvi
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a303319a3f87f684b72bcddd8d30cd4a2c60642
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351517"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referência de configurações de acesso condicional do Active Directory do Azure | Microsoft Docs

É possível utilizar o acesso condicional do [Azure AD (Azure Active Directory)](../active-directory-conditional-access-azure-portal.md) para ajustar como os usuários autorizados podem acessar os recursos.   

Este artigo fornece informações de suporte para as seguintes opções de configuração em uma política de acesso condicional: 

- Atribuições de aplicativos em nuvem

- Condição de plataforma de dispositivo 

- Condição de aplicativos cliente

- Requisito de aplicativo cliente aprovado


Se essa não é a informação que você está procurando, deixe um comentário no final deste artigo.

## <a name="cloud-apps-assignments"></a>Atribuições de aplicativos de nuvem

Com as políticas de acesso condicional, você controla como os usuários acessam seus [aplicativos de nuvem](conditions.md#cloud-apps). Ao configurar uma política de acesso condicional, será necessário selecionar pelo menos um aplicativo na nuvem. 

![Selecione os aplicativos em nuvem para sua política](./media/technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicativos em nuvem da Microsoft

Você pode atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft:


- Azure Analysis Services
- Azure DevOps
- Banco de dados SQL do Azure e o Data Warehouse - [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Análise do Microsoft Application Insights
- Proteção de informações do Microsoft Azure – [Saiba mais](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gerenciamento do Microsoft Azure - [Saiba mais](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Gerenciamento de assinaturas do Microsoft Azure
- Microsoft Cloud App Security
- Portal de controle de acesso das ferramentas Microsoft Commerce
- Serviço de autenticação de ferramentas do Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Registro do Microsoft Intune
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Pesquisa da Microsoft no Bing
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft 
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway 
- Grupos do Outlook
- Project Online
- Skype for Business Online
- Rede virtual privada (VPN)
- Visual Studio App Center
- Windows Defender ATP




### <a name="other-applications"></a>Outros aplicativos 

Além de aplicativos de nuvem da Microsoft, você pode atribuir uma política de acesso condicional aos seguintes tipos de aplicativos de nuvem:

- Aplicativos conectados ao Azure AD - Microsoft Azure Active Directory

- Aplicativo SaaS (software como serviço) federado e pré-integrado

- Aplicativos que usam SSO (logon único) com senha

- Aplicativos de linha de negócios

- Aplicativos que usam o Proxy de Aplicativo do Azure AD - Microsoft Azure Active Directory


## <a name="device-platform-condition"></a>Condição de plataforma de dispositivo

Em uma política de acesso condicional, é possível configurar a condição de plataforma do dispositivo para vincular a política ao sistema operacional em um cliente. O acesso condicional do Azure AD dá suporte às seguintes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

-  Windows

- macOS


![Vincular a política de acesso ao sistema operacional do cliente](./media/technical-reference/41.png)





## <a name="client-apps-condition"></a>Condição de aplicativos cliente 

Em sua política de acesso condicional, você pode configurar a condição de [aplicativos cliente](conditions.md#client-apps) para vincular a política ao aplicativo cliente que iniciou uma tentativa de acesso. Defina a condição de aplicativos cliente para conceder ou bloquear acesso quando uma tentativa de acesso for realizada a partir dos seguintes tipos de aplicativos cliente:

- Navegador
- Aplicativos móveis e de da área de trabalho

![Controlar acesso para aplicativos cliente](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Navegadores com suporte 

Em sua política de acesso condicional, você pode selecionar **Navegadores** como o aplicativo cliente.

![Controlar acesso para navegadores com suporte](./media/technical-reference/05.png)

Essa configuração funciona com todos os navegadores. No entanto, para satisfazer uma política de dispositivo, como um requisito de conformidade de dispositivo, há suporte para os sistemas operacionais e navegadores a seguir:


| SO                     | Navegadores                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Intune Managed Browser                |
| Android                | Chrome, Intune Managed Browser                |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |
 


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Por que vejo no navegador solicitar que um certificado

No Windows 7, iOS, Android e macOS o AD do Azure identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo está registrado com o Azure AD.  Quando um usuário entra pela primeira vez por meio do navegador do usuário é solicitado a selecionar o certificado. O usuário deve selecionar esse certificado antes de usar o navegador.


#### <a name="chrome-support"></a>Suporte ao Chrome

Para obter suporte ao Chrome, na **Atualização do Windows 10 para Criadores (versão 1703)** ou posterior, instale [essa extensão](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Para implantar automaticamente essa extensão para os navegadores Chrome, crie a seguinte chave do registro:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|NOME | 1 |
|Type | REG_SZ (String) |
|Dados | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Para obter suporte ao Chrome no **Windows 8.1 e 7**, crie a seguinte chave do registro:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|NOME | 1 |
|Type | REG_SZ (String) |
|Dados | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Esses navegadores dão suporte à autenticação de dispositivo, permitindo que o dispositivo seja identificado e validado em relação a uma política. A verificação de dispositivo falha caso o navegador esteja sendo executado em modo privado. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e cliente de área de trabalho com suporte

Em sua política de acesso condicional, você pode selecionar **Aplicativos móveis e clientes de desktop** como o aplicativo cliente.


![Controlar acesso para aplicativos móveis ou clientes de área de trabalho com suporte](./media/technical-reference/06.png)


Essa configuração tem um impacto nas tentativas de acesso feitas a partir dos seguintes aplicativos móveis e clientes de desktop: 


| Aplicativos cliente| Serviço de Destino| Plataforma |
| --- | --- | --- |
| Aplicativo Remoto do Azure| Serviço de Aplicativo Remoto do Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS|
| Aplicativo Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, iOS e Android|
| Aplicativo de Calendário/Email/Pessoas, Outlook 2016 Outlook 2013 (com autenticação moderna)| Office 365 Exchange Online| Windows 10|
| Política de localização e MFA para aplicativos. Políticas baseadas em dispositivos não têm suporte.| Qualquer serviço de aplicativo de Meus Aplicativos| Android e iOS|
| Microsoft Teams Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente – Windows Desktop, iOS, Android, WP e cliente da Web| Equipes da Microsoft| Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS|
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Aplicativos do Office 2016, aplicativos universais do Office, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), suporte aos Grupos do Office planejado para o futuro, suporte aos aplicativos do SharePoint planejado para o futuro| Office 365 SharePoint Online| Windows 10|
| Office 2016 (somente Word, Excel, PowerPoint, OneNote). Suporte para OneDrive for Business planejado para o futuro| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online| Windows 10, macOS|
| Aplicativos móveis do Office| Office 365 SharePoint Online| Android, iOS|
| Aplicativo Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Outlook 2019| Office 365 SharePoint Online| Windows 10, macOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| macOS|
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Aplicativo móvel do Outlook| Office 365 Exchange Online| Android, iOS|
| Aplicativo PowerBI| Serviço PowerBI| Windows 10, Windows 8.1, Windows 7, Android e iOS|
| Skype for Business| Office 365 Exchange Online| Android, IOS|
| Aplicativo Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="support-for-legacy-authentication"></a>Suporte para autenticação herdada

Ao selecionar **Outros clientes**, é possível especificar uma condição que afeta os aplicativos que usam autenticação básica com protocolos de email, como IMAP, MAPI, POP, SMTP e aplicativos mais antigos do Office que não usam autenticação moderna.  

![Outros clientes](./media/technical-reference/11.png)

Para saber mais, confira [Aplicativos clientes](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Requisito de aplicativo cliente aprovado 

Em sua política de acesso condicional, você pode exigir que uma tentativa de acesso aos aplicativos da nuvem selecionados só possa ser feita a partir de um aplicativo cliente aprovado. 

![Controlar acesso para aplicativos cliente aprovados](./media/technical-reference/21.png)

Essa configuração se aplica aos seguintes aplicativos cliente:



- Proteção de Informações do Microsoft Azure
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala 
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft PowerBI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Equipes da Microsoft
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer





**Comentários**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.

- O requisito **Exigir o aplicativo do cliente aprovado**:

    - Fornece suporte apenas para iOS e Android para [condição de plataforma de dispositivo](#device-platform-condition).


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do acesso condicional, consulte [ O que é acesso condicional no Active Directory do Azure? ](../active-directory-conditional-access-azure-portal.md)
- Se estiver pronto para configurar políticas de acesso condicional em seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png


