---
title: "Referência técnica do acesso condicional ao Azure Active Directory | Microsoft Docs"
description: "Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: f3d8bdbfc29ca1008006837512c0e6ae8cb8f6fe
ms.contentlocale: pt-br
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica do acesso condicional ao Azure Active Directory

Com o [acesso condicional do Azure AD (Active Directory)](active-directory-conditional-access-azure-portal.md), você pode ajustar como usuários autorizados podem acessar seus recursos.  
Este tópico fornece informações de suporte para os seguintes itens de uma política de acesso condicional: 

- Atribuições de aplicativos de nuvem

- Condição de plataforma de dispositivo 

- Condição de aplicativos cliente

- Requisito de aplicativo cliente aprovado 



## <a name="cloud-apps-assignments"></a>Atribuições de aplicativos de nuvem

Ao configurar uma política de acesso condicional, você precisa [selecionar os aplicativos de nuvem aos quais sua política se aplica](active-directory-conditional-access-azure-portal.md#who). 

![Controle](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Aplicativos de nuvem da Microsoft

Você pode atribuir uma política de acesso condicional aos seguintes aplicativos de nuvem da Microsoft:

- Aplicativo Remoto do Azure

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inclui o OneDrive for Business)

- Microsoft Power BI 

- Visual Studio Team Services

- Equipes da Microsoft


### <a name="other-apps"></a>Outros aplicativos 

Além de aplicativos de nuvem da Microsoft, você pode atribuir uma política de acesso condicional aos seguintes tipos de aplicativos de nuvem:

- Aplicativos conectados ao Azure Active Directory (Azure AD)

- Aplicativos SaaS (software como serviço) federados e pré-integrados

- Aplicativos que usam SSO (logon único) com senha

- Aplicativos de linha de negócios

- Aplicativos que usam o Proxy de Aplicativo do Azure AD. 


## <a name="device-platforms-condition"></a>Condição de plataformas de dispositivo

Em uma política de acesso condicional, você pode configurar a condição de plataforma de dispositivo para associar a política ao sistema operacional que está sendo executado em um cliente.

![Controle](./media/active-directory-conditional-access-technical-reference/41.png)

O acesso condicional do Azure AD dá suporte às seguintes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS (versão prévia)



## <a name="client-apps-condition"></a>Condição de aplicativos cliente 

Quando você configura uma política de acesso condicional, pode definir um [condição de aplicativos de cliente](active-directory-conditional-access-azure-portal.md#client-apps). A condição de aplicativos de cliente permite que você conceda ou bloqueie o acesso quando uma tentativa de acesso for feita desses tipos de aplicativos de cliente:

- Navegador
- Aplicativos móveis e de da área de trabalho

![Controle](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Navegadores com suporte 

Se você selecionar *Navegadores* em sua política de acesso condicional para conceder acesso aos recursos, o acesso será concedido apenas quando for feita a tentativa de acesso usando um navegador com suporte. Quando uma tentativa de acesso for feita usando um navegador sem suporte, a tentativa será bloqueada.

![Navegadores com suporte](./media/active-directory-conditional-access-technical-reference/05.png)

Em sua política de acesso condicional, há suporte para os seguintes navegadores: 


| SO                     | Navegadores                 | Suporte     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE, Edge                 | ![Verificação][1] |
| Win 10                 | Chrome                   | Visualização     |
| Win 8 / 8.1            | IE, Chrome               | ![Verificação][1] |
| Win 7                  | IE, Chrome               | ![Verificação][1] |
| iOS                    | Safari                   | ![Verificação][1] |
| Android                | Chrome                   | ![Verificação][1] |
| Windows Phone          | IE, Edge                 | ![Verificação][1] |
| Windows Server 2016    | IE, Edge                 | ![Verificação][1] |
| Windows Server 2016    | Chrome                   | Em breve |
| Windows Server 2012 R2 | IE, Chrome               | ![Verificação][1] |
| Windows Server 2008 R2 | IE, Chrome               | ![Verificação][1] |
| Mac OS                 | Safari                   | ![Verificação][1] |
| Mac OS                 | Chrome                   | Em breve |

> [!NOTE]
> Para obter suporte do Chrome, você precisa estar usando a Atualização do Windows 10 para Criadores e instalar a extensão encontrada [aqui](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).


### <a name="supported-mobile-apps-and-desktop-clients"></a>Aplicativos móveis e clientes de área de trabalho com suporte

Se você selecionar **Aplicativos móveis e clientes de área de trabalho** em sua política de acesso condicional para conceder acesso aos recursos, o acesso será concedido apenas quando for feita a tentativa de acesso usando aplicativos móveis ou clientes de área de trabalho com suporte. Quando uma tentativa de acesso for feita usando um aplicativo móvel ou cliente de área de trabalho sem suporte, a tentativa será bloqueada.

![Controle](./media/active-directory-conditional-access-technical-reference/06.png)

Os aplicativos móveis e clientes de área de trabalho a seguir oferecem suporte ao acesso condicional para o Office 365 e outros aplicativos de serviço conectados ao Azure AD:


| Aplicativos cliente| Serviço de Destino| Plataforma |
| --- | --- | --- |
| Política de localização e MFA para aplicativos. Políticas baseadas em dispositivos não têm suporte.| Qualquer serviço de aplicativo de Meus Aplicativos| Android e iOS|
| Aplicativo Remoto do Azure| Serviço de Aplicativo Remoto do Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X|
| Aplicativo Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| Microsoft Teams Services – controla todos os serviços que dão suporte ao Microsoft Teams e todos os seus aplicativos cliente – Windows Desktop, iOS, Android, WP e cliente da Web| Equipes da Microsoft| Windows 10, Windows 8.1, Windows 7, iOS e Android|
| Aplicativo de Calendário/Email/Pessoas, Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Aplicativo móvel do Outlook| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| Mac OS X|
| Aplicativos do Office 2016, aplicativos universais do Office, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), suporte aos Grupos do Office planejado para o futuro, suporte aos aplicativos do SharePoint planejado para o futuro| Office 365 SharePoint Online| Windows 10|
| Aplicativos do Office 2016, Office 2013 (com autenticação moderna), cliente de sincronização do OneDrive (veja as [observações](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Aplicativos móveis do Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 para macOS (somente Word, Excel, PowerPoint, OneNote). Suporte para OneDrive for Business planejado para o futuro| Office 365 SharePoint Online| Mac OS X|
| Aplicativo Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Aplicativo PowerBI. Atualmente, o aplicativo Power BI para Android não dá suporte ao acesso condicional baseado no dispositivo.| Serviço PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Aplicativo Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS e Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicativo cliente aprovado 

Quando você configura uma política de acesso condicional, você pode selecionar a necessidade de conceder acesso somente se uma tentativa de conexão foi feita por um aplicativo cliente aprovado. 

![Controle](./media/active-directory-conditional-access-technical-reference/21.png)

Os aplicativos de cliente aprovados para essa configuração são:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype for Business

- Equipes da Microsoft

- Microsoft Visio

- Microsoft Word


**Comentários:**

- Esses aplicativos dão suporte ao MAM (gerenciamento de aplicativo móvel) do Microsoft Intune.

- Esse requisito:

    - Só dá suporte a IOS e Android como a [condição de plataformas de dispositivo](#device-platforms-condition) selecionada 

    - Não dá suporte a **Navegador** como a [condição do aplicativo cliente](#supported-browsers) selecionada 
    
    - Substitui a [condição do aplicativo cliente](#supported-mobile-apps-and-desktop-clients) de **aplicativos móveis e clientes de área de trabalho** se ela for selecionada  


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do acesso condicional, confira [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- Se você estiver pronto para configurar políticas de acesso condicional em seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



