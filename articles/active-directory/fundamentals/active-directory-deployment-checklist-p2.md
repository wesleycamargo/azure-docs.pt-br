---
title: Lista de verificação do recurso de licenciamento Premium P2 do Azure Active Directory | Microsoft Docs
description: Implantar lista de verificação para a implantação do recurso Azure Active Directory Premium P2 em 30 dias, 90 dias e mais.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4fcb692d7189c84e32f55995538ffc692cb67dd6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064705"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Lista de verificação do recurso de licenciamento do Azure Active Directory Premium P2

Pode parecer desanimador implantar o Azure Active Directory (Azure AD) para sua organização e mantê-lo seguro. Este artigo identifica algumas tarefas comuns que os clientes acham úteis. Os clientes normalmente concluem essas tarefas durante o período de 30 dias, 90 dias, ou além para aprimorar sua postura de segurança. Mesmo as organizações que já implantaram o Microsoft Azure Active Directory podem usar essa lista de verificação para garantir que estejam obtendo o máximo do investimento.

Uma infraestrutura de identidade bem planejada e executada abre o caminho para um acesso mais seguro às suas cargas de trabalho de produtividade e dados apenas por usuários e dispositivos autenticados.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que você tenha licenças do Azure AD Premium P2, Enterprise Mobility + Security E5, Microsoft 365 E5 ou um pacote de licenças semelhante.

[Licenciamento do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Planejar e implantar: Dia 1 a 30

- Designar mais de um administrador global (conta break-glass)
   - [Gerenciar contas administrativas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md)
- Ativar o Azure AD Privileged Identity Management (PIM) para exibir relatórios
   - [Começar a usar o PIM](../privileged-identity-management/pim-getting-started.md)
- Use funções administrativas não globais, sempre que possível.
   - [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Autenticação
   - [Distribuir a redefinição de senha de autoatendimento](../authentication/howto-sspr-deployment.md)
   - Implantar a proteção de senha do Azure AD (visualização)
      - [Elimine senhas ruins em sua organização](../authentication/concept-password-ban-bad.md)
      - [Impor proteção de senha do Azure AD para o Active Directory do Windows Server](../authentication/concept-password-ban-bad-on-premises.md)
   - Configurar políticas de bloqueio de conta
      - [Bloqueio inteligente do Azure Active Directory](../authentication/howto-password-smart-lockout.md)
      - [Bloqueio de Extranet do AD FS e Extranet Smart Lockout](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Implantar a autenticação de vários fatores do Azure AD usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md)
   - [Habilite o registro convergente para redefinição de senha de autoatendimento e Autenticação de Vários Fator do Azure AD (visualização)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Ativar a proteção de identidade do Active Directory do Azure](../identity-protection/enable.md)
      - [Use eventos de risco para acionar a autenticação de vários fatores e alterações de senha](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Diretrizes de senha](https://www.microsoft.com/research/publication/password-guidance/)
      - Manter um requisito de comprimento mínimo de oito caracteres, mais não é necessariamente melhor.
      - Elimine requisitos de composição de caracteres.
      - [Elimine as redefinições de senha periódicas obrigatório para contas de usuário.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Sincronizar usuários do Active Directory no local
   - [Instalar o Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implementar a sincronização de Hash de senha](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Implementar o write-back de senha](../authentication/howto-sspr-writeback.md)
   - [Implementar o Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Atribuir licenças aos usuários por associação de grupo no Active Directory do Azure](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Planejar e implantar: Dia 31 a 90

- [Plano de usuário para acesso de convidado](../b2b/what-is-b2b.md)
   - [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](../b2b/add-users-administrator.md)
   - [Permitir ou bloquear convites para usuários B2B de organizações específicas](../b2b/allow-deny-list.md)
   - [Conceder aos usuários B2B do Microsoft Azure AD acesso aos aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md)
- Tome decisões sobre a estratégia de gerenciamento do ciclo de vida do usuário
- [Decida a estratégia de gerenciamento de dispositivos](../devices/overview.md)
   - [Cenários de uso e considerações de implantação para a Junção do Azure AD](../devices/azureadjoin-plan.md)
- [Gerencie o Windows Hello for Business na sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Planejar e implantar: Dia 90 e acima

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [Definir configurações de função do diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Atribuir funções de diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Concluir uma revisão de acesso para funções de diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Gerenciar o ciclo de vida do usuário de forma holística
   - O Azure AD tem uma abordagem para gerenciar o ciclo de vida da identidade
   - Remova etapas manuais do ciclo de vida da sua conta de funcionário para impedir o acesso não autorizado:
      - Sincronize identidades da sua fonte de verdade (HR System) para o Azure AD.
      - [Use os grupos dinâmicos para atribuir automaticamente os usuários aos grupos com base em seus atributos do RH (ou sua fonte de verdade), como departamento, título, região e outros atributos.](../users-groups-roles/groups-dynamic-membership.md)
      - [Use o provisionamento de gerenciamento de acesso baseado em grupo para provisionar automaticamente os usuários para aplicativos SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Próximas etapas

[Configurações de acesso à identidade e ao dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Políticas de acesso a dispositivos e identidades recomendadas comuns](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
