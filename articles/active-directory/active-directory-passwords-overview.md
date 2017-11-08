---
title: "Visão geral da redefinição de senha de autoatendimento do Azure AD | Microsoft Docs"
description: "O que a redefinição de senha de autoatendimento no Azure AD faz para sua organização?"
services: active-directory
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
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Redefinição de senha de autoatendimento no Azure AD para o profissional de TI

A SSPR (redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory) permite que os usuários redefinam suas senhas quando e onde precisarem, permitindo que os administradores controlem como as senhas são redefinidas. Os usuários não precisam mais chamar o suporte técnico para redefinir suas senhas.

* **Alteração de senha de autoatendimento** – o usuário sabe sua senha, mas deseja alterá-la para uma nova.
* **Redefinição de senha de autoatendimento** – o usuário não consegue se conectar e deseja redefinir sua senha usando um ou mais dos seguintes métodos de autenticação validados.
   * Mensagem de texto para um telefone celular validado
   * Chamada telefônica para um telefone celular ou comercial validado
   * Email para uma conta de email secundária validada
   * Respostas para as perguntas de segurança
* **Desbloqueio de conta de autoatendimento** – o usuário não consegue se conectar e sua senha foi bloqueada e gostaria de desbloquear a conta sem a intervenção do administrador usando os métodos de autenticação.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Por que escolher a redefinição da senha por autoatendimento do Azure AD

* **Reduzir os custos**, pois a redefinição de senha com auxílio do suporte técnico representa normalmente 20% das chamadas de suporte de uma organização de TI. 
* **Aprimorar as experiências do usuário final** e **reduzir o volume da assistência técnica** fornecendo aos usuários finais a capacidade de resolver seus próprios problemas de senha, sem ligar para a assistência técnica ou abrir uma solicitação de suporte.
* **Gerar mobilidade**, pois os usuários podem redefinir suas senhas onde quer que estejam.
* **Manter o controle** da política de segurança. Os administradores podem continuar a aplicar as políticas que têm hoje.

Se você estiver pronto, comece com o Azure AD SSPR usando nosso [guia de início rápido](active-directory-passwords-getting-started.md) e faça com que seus usuários redefinam rapidamente suas próprias senhas.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilidade da redefinição da senha por autoatendimento do Azure AD

A redefinição de senha por autoatendimento do Azure AD está disponível em três camadas, dependendo de sua assinatura.

* **Azure AD Gratuito**: os administradores somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Basic** ou qualquer **assinatura paga do Office 365** – os usuários somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Premium**: qualquer usuário ou administrador, incluindo somente nuvem, federado ou usuários com sincronização de senha, pode redefinir sua própria senha. As senhas locais exigem a habilitação do write-back de senha.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Preços do Azure AD, SLA, atualizações e roteiro

Mais detalhes sobre preços, licenciamento e planos futuros podem ser encontrados nos sites a seguir.

* [**Detalhes de preços do Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Preços do Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contratos de Nível de Serviço do Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contrato de Nível de Serviço do Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Atualizações do Azure**](https://azure.microsoft.com/updates/)
* [**Roteiro do Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Próximas etapas

* Você está pronto para começar a usar a SSPR? [Configuração da redefinição de senha de autoatendimento do Azure AD](active-directory-passwords-getting-started.md).
* Planeje uma implantação de SSPR bem-sucedida para seus usuários usando as diretrizes encontradas em nosso [**guia distribuição**](active-directory-passwords-best-practices.md).
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).