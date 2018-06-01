---
title: Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory | Microsoft Docs
description: Discute o compartilhamento de recursos com os parceiros externos usando a colaboração B2B do Microsoft Azure Active Directory e de O365.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d55d587022b763a6890c098dd0a1b9bef2a3b7fb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266942"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory

O compartilhamento externo no Office 365 (OneDrive, SharePoint Online, Unified Groups etc.) e a colaboração B2B do Azure AD (Azure Active Directory) são tecnicamente a mesma coisa. Todo compartilhamento externo, (com exceção do OneDrive/SharePoint Online), incluindo convidados em Grupos do Office 365, já usa as APIs de convite de colaboração B2B do Azure AD para compartilhamento.

O OneDrive/SharePoint Online tem um gerenciador de convite separado. Suporte para compartilhamento externo no OneDrive/SharePoint Online iniciado antes de o Azure AD ter desenvolvido o suporte. Ao longo do tempo, o compartilhamento externo do OneDrive/SharePoint Online acumulou vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. No entanto, há algumas diferenças sutis entre o funcionamento do compartilhamento externo do OneDrive/SharePoint Online e a colaboração do Azure AD B2B:

- O OneDrive/SharePoint Online adiciona usuários ao diretório depois que os usuários tiverem resgatado seus convites. Portanto, antes de resgate, você não vê o usuário no Portal do Azure AD. Se outro site convidar um usuário durante esse período, um novo convite será gerado. No entanto, quando você usa a colaboração B2B do Azure AD, os usuários são adicionados imediatamente no convite para que eles apareçam em todos os lugares.

- A experiência de resgate no OneDrive/SharePoint Online tem uma aparência diferente da experiência na colaboração B2B do Azure AD. Depois que um usuário resgata um convite, as experiências são parecidas.

- A colaboração B2B do Azure AD convidou usuários que possam ser escolhidos das caixas de diálogo de compartilhamento do OneDrive/SharePoint Online. Os usuários convidados do OneDrive/SharePoint Online também aparecem no Azure AD após o resgate do convite.

- Para gerenciar o compartilhamento externo no OneDrive/SharePoint Online junto com a colaboração B2B do Azure AD, defina a configuração do compartilhamento externo do OneDrive/SharePoint Online como **Permitir apenas o compartilhamento com usuários externos que já estão no diretório**. Os usuários podem acessar sites compartilhados externamente e escolher colaboradores externos adicionados pelo administrador. O administrador pode adicionar os colaboradores externos por meio de APIs de convite de colaboração B2B.

![As configurações de compartilhamento externo do OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
* [Delegação de convites de colaboração B2B](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)