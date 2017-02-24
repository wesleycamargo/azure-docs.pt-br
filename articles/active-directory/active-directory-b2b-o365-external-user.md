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
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Compartilhamento externo do Office 365 e colaboração B2B do Azure Active Directory

O compartilhamento externo no Office365 (OneDrive, SharePoint Online, Grupos Unificados etc.) e o uso da colaboração B2B do Azure Active Directory (Azure AD) são tecnicamente a mesma coisa. Todo compartilhamento externo, (com exceção do OneDrive / SharePoint Online), incluindo **Convidados em Grupos Unificados**, já está usando a API de convite de colaboração B2B do Azure AD para compartilhamento.

O OneDrive/SharePoint Online (ODSP) tem um gerenciador de convites separado, pois o suporte do ODSP para compartilhamento externo começou antes de ter suporte inerente como parte da malha do Azure AD. Ao longo do tempo, o compartilhamento externo do ODSP acumulou vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. Estamos trabalhando com o ODSP para integrá-lo às APIs de convite do Azure AD B2B (citadas nesta documentação), a fim de unificar todos os benefícios de ponta a ponta e acumular em todos eles as inovações de experiências feitas pelo Azure AD. Enquanto isso, há algumas diferenças sutis entre o funcionamento do compartilhamento externo do ODSP e do Azure AD B2B:

1. O ODSP adiciona o usuário ao diretório depois que o usuário tiver resgatado seu convite. Assim, até que o usuário resgate, você não verá o usuário no Portal do Azure AD. Durante esse período, se um usuário for convidado de outro site, um novo convite será gerado. No entanto, ao usar a colaboração B2B, podemos adicioná-lo imediatamente ao convite para que ele apareça em todos os lugares.

2. A experiência de resgate no ODSP parece diferente da colaboração B2B.

3. No entanto, após o resgate pelo usuário, eles parecem semelhantes.

4. Os usuários convidados da colaboração B2B podem ser escolhidos em caixas de diálogo de compartilhamento do ODSP. Os usuários convidados do ODSP também aparecem no Azure AD após o resgate do convite.

5. Para usar o compartilhamento externo no ODSP junto com a colaboração B2B de uma forma gerenciada e controlada pelo administrador, defina a configuração do compartilhamento externo do ODSP como **Permitir apenas o compartilhamento com usuários externos que já estão no diretório**. Os usuários podem acessar sites compartilhados externamente e escolher colaboradores externos adicionados pelo administrador. O administrador pode adicionar os colaboradores externos por meio de APIs de convite de colaboração B2B.

![configuração de compartilhamento externo do ODSP](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações do usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


