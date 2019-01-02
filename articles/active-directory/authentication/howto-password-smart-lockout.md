---
title: Evitando ataques de força bruta usando bloqueio inteligente do AD do Azure
description: Bloqueio inteligente de Active Directory do Azure ajuda a proteger sua organização contra ataques de força bruta tentando descobrir senhas
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 9f701f0f6d00d2913eaf5b237435fd375b2030e0
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435269"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Sync smart lockout

O bloqueio inteligente ajuda a bloquear atores mal-intencionados que estão tentando adivinhar as senhas dos usuários ou usar métodos de força bruta para entrada. Ele pode reconhecer entradas provenientes de usuários válidos e tratá-las de forma diferente do que as entradas dos invasores e de outras fontes desconhecidas. O bloqueio inteligente bloqueia os invasores, enquanto permite que os usuários continuem acessando suas contas e sendo produtivos.

Por padrão, o bloqueio inteligente bloqueia a conta de tentativas de login por um minuto após 10 tentativas malsucedidas. A conta é bloqueada novamente após cada tentativa de entrada com falha subsequente durante um minuto na primeira vez, e tempos mais longos em tentativas subsequentes.

O bloqueio inteligente rastreia os últimos três hashes de senha incorreta para evitar o reincremento do contador de bloqueio. Se alguém inserir a mesma senha incorreta várias vezes, esse comportamento não causará o bloqueio da conta.

 > [!NOTE]
 > A funcionalidade de acompanhamento de hash não está disponível para clientes com a autenticação de passagem habilitada, pois a autenticação ocorre localmente, não na nuvem.

O bloqueio inteligente está sempre ativado para todos os clientes do Azure AD com as configurações padrão que oferecem a combinação certa de segurança e usabilidade. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Basic ou superior licenças para seus usuários.

O uso do bloqueio inteligente não garante que um usuário genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de usuário, tentamos o nosso melhor para não bloquear o usuário genuíno. O serviço de bloqueio tenta garantir que atores mal-intencionados não obtenham acesso a uma conta de usuário original.  

* Cada centro de dados do Azure Active Directory rastreia o bloqueio de forma independente. Um usuário terá (threshold_limit * datacenter_count) número de tentativas, se o usuário acessar cada datacenter.
* O Smart Lockout usa localização familiar versus local desconhecido para diferenciar entre um ator ruim e o usuário genuíno. Locais desconhecidos e familiares ambos terá contadores separados de bloqueio.

Bloqueio inteligente pode ser integrado com implantações híbridas, usando a sincronização de hash de senha ou autenticação de passagem para proteger contas do Active Directory no local de bloqueio por invasores. Definindo políticas de bloqueio inteligente no AD do Azure adequadamente, ataques podem ser filtrados antes que elas atinjam o Active Directory no local.

Ao usar [autenticação de passagem](../hybrid/how-to-connect-pta.md), você precisará certificar-se de que:

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
1. Coloque a **duração do bloqueio em segundos**, para a duração em segundos de cada bloqueio. O padrão é 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro login depois do bloqueio também falhar, a conta será bloqueada de novo. Se uma conta bloquear repetidamente, o bloqueio aumentará a duração.

![Personalize a política de bloqueio inteligente do Azure AD no portal do Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Próximas etapas

[Descubra como banir senhas ruins na sua organização usando Azure AD.](howto-password-ban-bad.md)

[Configure reset de senhas self-service para permitir usuários desbloquearem suas próprias contas.](quickstart-sspr.md)