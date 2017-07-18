---
title: 'Licenciamento: SSPR do Azure AD | Microsoft Docs'
description: "Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 3ed13c819f8e32cab44013cdcbf1b3a921ba98b8
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---
# Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD
<a id="licensing-requirements-for-azure-ad-self-service-password-reset" class="xliff"></a>

Para a redefinição de senhas do AD do Azure funcionar, você **deverá ter pelo menos uma licença atribuída na sua organização**. Não impomos licenciamento por usuário na experiência de redefinição da senha. Para manter a conformidade com o contrato de licenciamento da Microsoft, você precisa atribuir licenças a todos os usuários que usam recursos premium.

* **Somente usuários de nuvem** – Qualquer SKU pago do Office 365 (O365) ou Azure AD Basic
* Usuários na **nuvem** e/ou **locais** – Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Secure Productive Enterprise (SPE)

## Licenças necessárias para write-back de senha
<a id="licenses-required-for-password-writeback" class="xliff"></a>

Para usar o write-back de senha, você deve ter uma das licenças a seguir atribuídas no locatário.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Secure Productive Enterprise E3
* Secure Productive Enterprise E5

> [!NOTE]
> Os planos de licenciamento do Standalone Office 365 **não dão suporte ao write-back de senha** e exigem um dos planos anteriores para que essa funcionalidade funcione.

As informações de licenciamento adicionais, inclusive custos, podem ser encontradas nas páginas a seguir

* [Site de Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

## Habilitar licenciamento com base em grupo ou usuário
<a id="enable-group-or-user-based-licensing" class="xliff"></a>

O Azure AD já dá suporte ao licenciamento com base em grupo, permitindo que os administradores atribuam licenças em massa a um grupo de usuários, em vez de atribuí-los um por vez. [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade “Local de uso” para o usuário. A atribuição de licenças pode ser feita em Usuário > Perfil > seção Configurações no portal do Azure. **Ao usar a atribuição de grupo de licenças, qualquer usuário sem um local de uso especificado herda o local do diretório.**

## Próximas etapas
<a id="next-steps" class="xliff"></a>

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início rápido** ](active-directory-passwords-getting-started.md) - Para deixar em funcionamento com o gerenciamento de senha de autoatendimento do Azure AD 
* [**Dados** ](active-directory-passwords-data.md) - Entender os dados necessários e como são usados para o gerenciamento de senhas
* [**Distribuição** ](active-directory-passwords-best-practices.md) -planeje e implante a SSPR para seus usuários usando as diretrizes encontradas aqui
* [**Personalização** ](active-directory-passwords-customize.md) - Personalize a aparência da experiência da SSPR para sua empresa.
* [**Relatório** ](active-directory-passwords-reporting.md) - Descubra se, quando e onde os usuários estão acessando a funcionalidade da SSPR
* [**Aprofundamento técnico** ](active-directory-passwords-how-it-works.md) - Entenda como ele funciona
* [**Perguntas frequentes (FAQ)**](active-directory-passwords-faq.md) - Como? Por quê? O quê? Onde? Quem? Quando? - Respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR


