---
title: Evitando ataques de força bruta usando bloqueio inteligente do AD do Azure
description: Bloqueio inteligente de Active Directory do Azure ajuda a proteger sua organização contra ataques de força bruta tentando descobrir senhas
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034177"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Sync smart lockout

Bloqueio inteligente usa inteligência de nuvem para bloquear os atores ruins que estão tentando adivinhar as senhas dos usuários ou usar métodos de força bruta para entrar. Essa inteligência pode reconhecer entradas provenientes de usuários válidos e tratá-los de forma diferente do que os invasores e outras fontes desconhecidas. Bloqueio inteligente pode bloquear os invasores ao mesmo tempo permitindo que os usuários continuam a acessar suas contas e produtividade.

Bloqueio inteligente está sempre ativado para todos os clientes do AD do Azure com as configurações padrão que oferecem a combinação certa de segurança e facilidade de uso. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Basic ou superior licenças para seus usuários.

Bloqueio inteligente pode ser integrado com implantações híbridas, usando a sincronização de hash de senha ou autenticação de passagem para proteger contas do Active Directory no local de bloqueio por invasores. Definindo políticas de bloqueio inteligente no AD do Azure adequadamente, ataques podem ser filtrados antes que elas atinjam o Active Directory no local.

Ao usar [autenticação de passagem](../connect/active-directory-aadconnect-pass-through-authentication.md), você precisará certificar-se de que:

   * O limite de bloqueio do Azure AD seja **menor** que o limite de bloqueio da conta do Active Directory. Defina os valores de forma que o limite de bloqueio da conta do Active Directory seja pelo menos duas ou três vezes maior do que o limite de bloqueio do Azure AD. 
   * A duração de bloqueio do Azure AD **em segundos** é **maior** que a duração de redefinir contador de bloqueios de conta após do Active Directory em **minutos**.

> [!IMPORTANT]
> No momento um administrador não pode desbloquear contas de nuvem dos usuários se eles foram bloqueados fora da funcionalidade do Bloqueio Inteligente. O administrador deve aguardar a duração do bloqueio expirar.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificar políticas de bloqueio de conta no local

Use as instruções a seguir para verificar suas políticas de bloqueio de conta no local do Active Directory:

1. Abra a ferramenta de Gerenciamento de Política de Grupo.
2. Editar a política de grupo que inclui a diretiva de bloqueio de conta da sua organização, por exemplo, o **Default Domain Policy**.
3. Navegue até **Configuração do Computador** > **Políticas** > **Configurações do Windows** > **Configurações de Segurança** > **Políticas de Conta** > **Política de Bloqueio de Conta**.
4. Verifique os valores de **Limite de bloqueio de conta** e **Redefinir contador de bloqueios de conta após**.

![Modificar a política de bloqueio de conta do local do Active Directory usando um objeto de diretiva de grupo](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerenciar valores de bloqueio inteligente do Azure AD

Baseado nos seus requisitos organizacionais, os valores de bloqueio inteligente talvez precisem ser personalizados. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Basic ou superior licenças para seus usuários.

Para verificar ou modificar os valores de bloqueio inteligente para a sua organização, use os seguintes passos:

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**, então **Métodos de Autenticação**.
1. Coloque o **limite de bloqueio**, baseado em quantas vezes de logins falhados é permitido em uma conta antes do primeiro bloqueio. O padrão é 10.
1. Coloque a **duração do bloqueio em segundos**, para a duração em segundos de cada bloqueio.

> [!NOTE]
> Se o primeiro login depois do bloqueio também falhar, a conta será bloqueada de novo. Se uma conta bloquear repetidamente, o bloqueio aumentará a duração.

![Personalize a política de bloqueio inteligente do Azure AD no portal do Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Próximas etapas

[Descubra como banir senhas ruins na sua organização usando Azure AD.](howto-password-ban-bad.md)

[Configure reset de senhas self-service para permitir usuários desbloquearem suas próprias contas.](quickstart-sspr.md)