---
title: Referência de configurações de acesso condicional do Active Directory do Azure | Microsoft Docs
description: Obtenha uma visão geral das configurações com suporte em uma política de acesso condicional do Active Directory do Azure.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 2f9075cef671128cacc37f16e8bf29bf0f60401d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619632"
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

- Proteção de Informações do Azure - [Saiba mais](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- RemoteApp do Azure

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inclui o OneDrive for Business e o Project Online)

- Microsoft Power BI 

- Azure DevOps

- Equipes da Microsoft


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


| SO                     | Navegadores                            | Suporte     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     | ![Verificação][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Verificação][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Verificação][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Verificação][1] |
| Android                | Chrome, Intune Managed Browser      | ![Verificação][1] |
| Windows Phone          | Internet Explorer, Microsoft Edge             | ![Verificação][1] |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             | ![Verificação][1] |
| Windows Server 2016    | Chrome                              | Em breve |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Verificação][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Verificação][1] |
| macOS                  | Chrome, Safari                      | ![Verificação][1] |



#### <a name="chrome-support"></a>Suporte ao Chrome

Para obter suporte ao Chrome, na **Atualização do Windows 10 para Criadores (versão 1703)** ou posterior, instale [essa extensão](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Para implantar automaticamente essa extensão para os navegadores Chrome, crie a seguinte chave do registro:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|NOME | 1 |
|Tipo | REG_SZ (String) |
|Dados | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Para obter suporte ao Chrome no **Windows 8.1 e 7**, crie a seguinte chave do registro:

|    |    |
|--- | ---|
|Caminho | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|NOME | 1 |
|Tipo | REG_SZ (String) |
|Dados | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Esses navegadores dão suporte à autenticação de dispositivo, permitindo que o dispositivo seja identificado e validado em relação a uma política. A verificação de dispositivo falha caso o navegador esteja sendo executado em modo privado. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Aplicativos móveis e cliente de área de trabalho com suporte

Em sua política de acesso condicional, você pode selecionar **Aplicativos móveis e clientes de desktop** como o aplicativo cliente.


![Controlar acesso para aplicativos móveis ou clientes de área de trabalho com suporte](./media/technical-reference/06.png)


Essa configuração tem um impacto nas tentativas de acesso feitas a partir dos seguintes aplicativos móveis e clientes de desktop: 


|Aplicativos cliente|Serviço de Destino|Plataforma|
|---|---|---|
|Aplicativo Remoto do Azure|Serviço de Aplicativo Remoto do Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
|Aplicativo Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, iOS e Android|
|Aplicativo de Calendário/Email/Pessoas, Outlook 2016 Outlook 2013 (com autenticação moderna)|Office 365 Exchange Online|Windows 10|
|Política de localização e MFA para aplicativos. Políticas baseadas em dispositivos não têm suporte. |Qualquer serviço de aplicativo de Meus Aplicativos|Android e iOS|
|Microsoft Teams Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente – Windows Desktop, iOS, Android, WP e cliente da Web|Equipes da Microsoft|Windows 10, Windows 8.1, Windows 7, iOS, Android e macOS |
|Aplicativos do Office 2016, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Aplicativos do Office 2016, aplicativos universais do Office, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), suporte aos Grupos do Office planejado para o futuro, suporte aos aplicativos do SharePoint planejado para o futuro|Office 365 SharePoint Online|Windows 10|
|Office 2016 para macOS (somente Word, Excel, PowerPoint, OneNote). Suporte para OneDrive for Business planejado para o futuro|Office 365 SharePoint Online|Mac OS X|
|Aplicativos móveis do Office|Office 365 SharePoint Online|Android, iOS|
|Aplicativo Office Yammer|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office para macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Aplicativo móvel do Outlook|Office 365 Exchange Online|Android, iOS|
|Aplicativo PowerBI|Serviço PowerBI|Windows 10, Windows 8.1, Windows 7, Android e iOS|
|Skype for Business|Office 365 Exchange Online|Android, IOS |
|Aplicativo do Azure DevOps|Azure DevOps|Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicativo cliente aprovado 

Em sua política de acesso condicional, você pode exigir que uma tentativa de acesso aos aplicativos da nuvem selecionados só possa ser feita a partir de um aplicativo cliente aprovado. 

![Controlar acesso para aplicativos cliente aprovados](./media/technical-reference/21.png)

Essa configuração se aplica aos seguintes aplicativos cliente:


- Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft Invoicing
- Microsoft Launcher
- Proteção de Informações do Microsoft Azure
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Equipes da Microsoft
- Microsoft Visio
- Microsoft Word
- Microsoft To-Do
- Microsoft Stream
- Microsoft Edge



**Comentários**

- Os aplicativos cliente aprovados fornecem suporte ao recurso de gerenciamento de aplicativo móvel Intune.

- O requisito **Exigir o aplicativo do cliente aprovado**:

    - Fornece suporte apenas para iOS e Android para [condição de plataforma de dispositivo](#device-platforms-condition).


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do acesso condicional, consulte [ O que é acesso condicional no Active Directory do Azure? ](../active-directory-conditional-access-azure-portal.md)
- Se estiver pronto para configurar políticas de acesso condicional em seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png


