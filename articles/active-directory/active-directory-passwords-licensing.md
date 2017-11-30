---
title: 'Licenciamento: SSPR do Azure AD | Microsoft Docs'
description: "Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b65a4e49097828e7cd54a29e814befd2d2ac5d88
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD

Para a redefinição de senhas do Azure AD (Azure Active Directory) funcionar, você *deve ter pelo menos uma licença atribuída na sua organização*. Não impomos licenciamento por usuário na experiência de redefinição da senha. Para manter a conformidade com o contrato de licenciamento da Microsoft, você precisa atribuir licenças a todos os usuários que usam recursos premium.

* **Somente usuários de nuvem**: Qualquer SKU pago do Office 365 ou Azure AD Basic
* Usuários na **nuvem** ou **locais**: Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licenças necessárias para write-back de senha

Para usar o write-back de senha, você deve ter uma das licenças a seguir atribuídas no locatário:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (plano E3)
* Microsoft 365 (plano E5)

> [!WARNING]
> Os planos de licenciamento do Standalone Office 365 *não dão suporte ao write-back de senha* e exigem que você tenha um dos planos anteriores para que essa funcionalidade funcione.

As informações de licenciamento adicionais, inclusive custos, podem ser encontradas nas páginas a seguir:

* [Site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licenciamento com base em grupo ou usuário

O Azure AD agora é compatível com o licenciamento baseado em grupo. Os administradores podem atribuir licenças em massa a um grupo de usuários, em vez de atribuí-las uma por vez. Para obter mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade **Local de uso** para o usuário. A atribuição de licenças pode ser feita na seção **Usuário** > **Perfil** > **Configurações** no portal do Azure. *Ao usar a atribuição de licença de grupo, qualquer usuário sem um local de uso especificado herda o local do diretório.*

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

