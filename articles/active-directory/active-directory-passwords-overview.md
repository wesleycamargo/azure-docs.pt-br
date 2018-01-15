---
title: "Visão geral da redefinição de senha de autoatendimento do Azure AD | Microsoft Docs"
description: "O que a redefinição de senha de autoatendimento no Azure AD faz para sua organização?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 074a22d0d3d6f4218be431409dcb0e516d226335
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Redefinição de senha de autoatendimento no Azure AD para o profissional de TI

Com a redefinição de senha de autoatendimento (SSPR) do Azure Active Directory (Azure AD), os usuários podem redefinir suas senhas por si só quando e onde eles precisam. Ao mesmo tempo, os administradores podem controlar como a redefinição de senha do usuário ocorre. Os usuários não precisam mais chamar o suporte técnico para redefinir suas senhas. SSPR do Azure AD inclui:

* **Alteração de senha de autoatendimento**: o usuário sabe sua senha, mas deseja alterá-la para uma nova.
* **Redefinição de senha de autoatendimento**: o usuário não consegue se conectar e deseja redefinir sua senha usando um ou mais dos seguintes métodos de autenticação validados:
   * Envie uma mensagem de texto para um celular validado.
   * Faça uma chamada telefônica para um celular ou comercial validado.
   * Envie um email para uma conta de email secundária validada.
   * Respostas para as perguntas de segurança.
* **Desbloqueio de conta de autoatendimento**: O usuário não consegue entrar com sua senha e foi bloqueado. O usuário deseja desbloquear suas contas sem a intervenção do administrador usando os métodos de autenticação.

## <a name="why-choose-azure-ad-sspr"></a>Por que escolher o Azure AD SSPR

O Azure AD SSPR ajuda a:

* **Reduzir os custos**, pois a redefinição de senha com auxílio do suporte técnico representa normalmente 20% das chamadas de suporte de uma organização de TI. 
* **Aprimorar as experiências do usuário final** e **reduzir o volume de suporte técnico**, permitindo que os usuários finais resolvam seus próprios problemas com senhas. Não é necessário chamar um suporte técnico ou abrir uma solicitação de suporte.
* **Gerar mobilidade**, pois os usuários podem redefinir suas senhas onde quer que estejam.
* **Manter o controle** da política de segurança. Os administradores podem continuar a aplicar as políticas que têm hoje.

Se você estiver pronto, poderá começar com o Azure AD SSPR usando nosso [guia de início rápido](active-directory-passwords-getting-started.md). Você pode dar a seus usuários rapidamente a capacidade de redefinir suas próprias senhas.

## <a name="azure-ad-sspr-availability"></a>Disponibilidade do Azure AD SSPR

O Azure AD SSPR está disponível em três camadas, dependendo de sua assinatura:

* **Azure AD Gratuito**: os administradores somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Basic** ou qualquer **assinatura paga do Office 365**: os usuários somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Premium**: qualquer usuário ou administrador, incluindo somente nuvem, federado ou usuários com sincronização de senha, pode redefinir sua própria senha. As senhas locais exigem a habilitação do write-back de senha.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Preços do Azure AD, SLA, atualizações e roteiro

Mais detalhes sobre preços, licenciamento e planos futuros podem ser encontrados nos sites a seguir:

* [Detalhes de preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Preços do Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/)
* [Contrato de Nível de Serviço do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Atualizações do Azure](https://azure.microsoft.com/updates/)
* [Roteiro do Azure](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Próximas etapas

* Você está pronto para começar a usar a SSPR? [Redefinição da senha de autoatendimento do Azure AD](active-directory-passwords-getting-started.md).
* Planeje uma implantação de SSPR bem-sucedida para seus usuários usando as diretrizes encontradas em nosso [guia distribuição](active-directory-passwords-best-practices.md).
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
