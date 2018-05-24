---
title: Redefinição de senha por autoatendimento da licença – Azure Active Directory
description: Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 78d4d721f2821a8365185c0bad6d795c67a75292
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33864644"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD

Para a redefinição de senhas do Azure AD (Azure Active Directory) funcionar, você *deve ter pelo menos uma licença atribuída na sua organização*. Não impomos licenciamento por usuário na experiência de redefinição da senha. Uma licença apropriada é necessária se um usuário se beneficia direta ou indiretamente de qualquer recurso coberto por essa licença.

* **Somente usuários de nuvem**: Qualquer SKU pago do Office 365 ou Azure AD Basic
* Usuários na **nuvem** ou **locais**: Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>Licenças necessárias para write-back de senha

Para usar o write-back de senha, você deve ter uma das licenças a seguir atribuídas no locatário:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5
* Microsoft 365 F1

> [!WARNING]
> Os planos de licenciamento do Standalone Office 365 *não dão suporte ao write-back de senha* e exigem que você tenha um dos planos anteriores para que essa funcionalidade funcione.
>

As informações de licenciamento adicionais, inclusive custos, podem ser encontradas nas páginas a seguir:

* [Site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licenciamento com base em grupo ou usuário

O Azure AD agora é compatível com o licenciamento baseado em grupo. Os administradores podem atribuir licenças em massa a um grupo de usuários, em vez de atribuí-las uma por vez. Para obter mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade **Local de uso** para o usuário. A atribuição de licenças pode ser feita na seção **Usuário** > **Perfil** > **Configurações** no portal do Azure. *Ao usar a atribuição de licença de grupo, qualquer usuário sem um local de uso especificado herda o local do diretório.*

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../active-directory-passwords-reset-register.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)
