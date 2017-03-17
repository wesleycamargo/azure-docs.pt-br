---
title: "Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "referência ao mapeamento de declarações para colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 6c8d7dc7d070ce65cbe637359bf7933eac68ce23
ms.openlocfilehash: ae87c3487abd194c524d067a2a9b7bb9f8df8ac7
ms.lasthandoff: 02/24/2017


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory

O compartilhamento externo no Office 365 (OneDrive, SharePoint Online, Grupos Unificados etc.) e a colaboração business-to-business (B2B) do Azure Active Directory (Azure AD) são tecnicamente a mesma coisa. Todo compartilhamento externo, (com exceção do OneDrive/SharePoint Online), incluindo Convidados em Grupos Unificados, já usa as APIs de convite de colaboração B2B do Azure AD para compartilhamento.

O OneDrive/SharePoint Online tem um gerenciador de convite separado. Suporte para compartilhamento externo no OneDrive/SharePoint Online iniciado antes de o Azure AD ter desenvolvido o suporte. Ao longo do tempo, o compartilhamento externo do OneDrive/SharePoint Online acumulou vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. Estamos trabalhando com o OneDrive/SharePoint Online para adicionar as APIs de convite do Azure AD B2B (citadas nesta documentação), a fim de unificar o processo em todos os produtos e adotar todas as inovações feitas pelo Azure AD. Enquanto isso, há algumas diferenças sutis entre o funcionamento do compartilhamento externo do OneDrive/SharePoint Online e a colaboração do Azure AD B2B:

- O OneDrive/SharePoint Online adiciona usuários ao diretório depois que os usuários tiverem resgatado seus convites. Portanto, antes de resgate, você não vê o usuário no Portal do Azure AD. Se outro site convidar um usuário durante esse período, um novo convite será gerado. No entanto, quando você usa a colaboração B2B do Azure AD, os usuários são adicionados imediatamente no convite para que eles apareçam em todos os lugares.

- A experiência de resgate no OneDrive/SharePoint Online tem uma aparência diferente da experiência na colaboração B2B do Azure AD. Depois que um usuário resgata um convite, as experiências são parecidas.

- A colaboração B2B do Azure AD convidou usuários que possam ser escolhidos das caixas de diálogo de compartilhamento do OneDrive/SharePoint Online. Os usuários convidados do OneDrive/SharePoint Online também aparecem no Azure AD após o resgate do convite.

- Para usar o compartilhamento externo no OneDrive/SharePoint Online junto com a colaboração B2B do Azure AD de uma forma gerenciada e controlada pelo administrador, defina a configuração do compartilhamento externo do OneDrive/SharePoint Online como **Permitir apenas o compartilhamento com usuários externos que já estão no diretório**. Os usuários podem acessar sites compartilhados externamente e escolher colaboradores externos adicionados pelo administrador. O administrador pode adicionar os colaboradores externos por meio de APIs de convite de colaboração B2B.

![As configurações de compartilhamento externo do OneDrive/SharePoint Online](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Como adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegação de convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações do usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)

