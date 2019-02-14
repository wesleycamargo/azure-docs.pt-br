---
title: Registro convergido para o Azure AD SSPR e o MFA (pré-visualização pública)
description: Autenticação de vários fatores do Azure AD e registro de redefinição de senha de autoatendimento (pré-visualização pública)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d4dc7e6fc1545e1de1e3b50887585c4156b4c0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208768"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Registro convergido para redefinição de senha de autoatendimento e autenticação multifator do Azure (visualização pública)

Até agora, os usuários precisavam registrar métodos de autenticação para a Autenticação Multifatores do Azure (MFA) e a redefinição de senha de autoatendimento (SSPR) em dois portais diferentes. Muitos usuários estavam confusos de que métodos semelhantes eram usados para o Azure MFA e o SSPR e não se registrariam em ambos os portais. Alguns usuários não puderam usar o Azure MFA ou SSPR quando necessário, levando a chamadas ao suporte técnico. 

Agora, os usuários podem se registrar uma vez e obter os benefícios do Azure MFA e SSPR. Os usuários não precisam registrar seus métodos de autenticação para esses recursos duas vezes.  

Antes de ativar essa nova experiência para sua organização, recomendamos que você analise este artigo e a [documentação do usuário](https://aka.ms/securityinfoguide) para entender o impacto que a experiência terá sobre seus usuários. Você pode usar a documentação do usuário para treinar e preparar seus usuários para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

|     |
| --- |
| O registro convergente para redefinição de senha de autoatendimento e a Autenticação de vários fatores do Azure é um recurso de visualização pública do Azure AD (Azure Active Directory). Para saber mais, veja [Termos de uso complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Para permitir que seus usuários registrem métodos de autenticação para a Autenticação de Vários Fator do Azure e a redefinição de senha de autoatendimento em uma única experiência, conclua as etapas a seguir:

1. Entre no portal do Azure como administrador global ou administrador de usuários.
2. Navegue até **Active Directory do Azure** >  **Configurações do usuário** >  **Gerenciar configurações dos recursos de visualização do painel de acesso**.
3. Em os **usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança**. Você pode optar por ativar um grupo de usuários **Selecionado** ou para **todos** os usuários.

Os usuários podem agora ir para [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) para registrar seus métodos de autenticação para MFA e SSPR. Para saber mais sobre o que seus usuários verão nessa nova experiência, consulte a [documentação do usuário](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Depois de ativar essa experiência, os usuários que se registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência poderão usá-los para MFA e SSPR, se esses métodos estiverem ativados nas políticas MFA e SSPR. Se você desabilitar essa experiência, os usuários que acessarem a página de registro anterior do SSPR em https: /aka.ms/ssprsetup serão obrigados a executar a autenticação multi fator antes que possam acessar a página.  

## <a name="how-it-works"></a>Como ele funciona

Se um usuário já tiver registrado métodos de autenticação por meio das experiências de registro MFA e SSPR separadas, ele não precisará registrar essas informações novamente. No entanto, se suas configurações exigirem que os usuários se registrem no MFA ou no SSPR, poderão ver um aviso para revisar suas informações de segurança ao fazer login.

Se um usuário tiver registrado um método que possa ser usado para o MFA, ele será solicitado a realizar a autenticação multi fator antes que possa acessar a nova experiência.

Se você tiver aplicado o registro para MFA ou SSPR e um usuário ainda não tiver registrado, ele será solicitado a se registrar quando fizer login.

Os usuários que serão solicitados a registrar ao entrar em ver a experiência do seguinte:

![Registro convergido. Configure os métodos como um novo usuário.](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Essa experiência é mostrada apenas quando um usuário é solicitado a registrar-se ao fazer login. Os usuários que acessam diretamente a experiência em https://aka.ms/setupsecurityinfo veem uma versão diferente da experiência, descrita posteriormente neste artigo.

Os métodos de autenticação exibidos são alterados com base nos métodos ativados nas políticas do MFA e do SSPR. O usuário é solicitado a registrar o número mínimo de métodos de autenticação necessários para estar em conformidade com a política de MFA, a política de SSPR ou ambos. Se houver flexibilidade em quais métodos de autenticação o usuário pode registrar, eles poderão selecionar **Escolher informações de segurança** para escolher outros métodos de autenticação.  

> [!NOTE]
> Se você ativar o uso de notificação de aplicativo móvel e código de aplicativo móvel, os usuários que registrarem o aplicativo Microsoft Authenticator por meio de uma notificação poderão usar notificação e código para confirmar sua identidade.

Ao contrário da experiência anterior de registro do MFA, os usuários não são solicitados a registrar uma senha de aplicativo quando passam pela nova experiência de registro. Em vez disso, eles devem seguir as etapas listadas no tutorial de senhas de aplicativos para registrar senhas de aplicativos na nova experiência.  

Depois que um usuário conclui o registro, o método MFA padrão é definido automaticamente. Se o usuário registrou um aplicativo autenticador, o método padrão é definido como app. Se o usuário não registrou um aplicativo autenticador e registrou apenas seu número de telefone, o método padrão é definido como chamada telefônica. Os usuários podem alterar seu padrão indo até https://aka.ms/setupsecurityinfo e selecionando **Change default**.  

Se o registro não for aplicado, os usuários poderão gerenciar seus próprios métodos de autenticação em https://aka.ms/setupsecurityinfo. Se um usuário tiver registrado anteriormente um método que possa ser usado para o MFA, ele será solicitado a executar a autenticação multi fator antes que possa acessar a página.  

![Registro convergido. Edite métodos como um usuário registrado.](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Nesta página, os usuários vêem métodos de autenticação e métodos de autenticação registrados anteriormente para eles, como o Office phone. Os usuários também podem adicionar, editar ou excluir seus métodos de autenticação (exceto o telefone do Office).  

Registros de auditoria para essa nova experiência existem na categoria Métodos de Autenticação do log de auditoria.  

## <a name="known-issues"></a>Problemas conhecidos

**O método MFA padrão é definido como chamada telefônica quando um usuário registra um telefone usando uma mensagem de texto**

   * Alguns usuários podem perceber que o método MFA padrão é definido como chamada telefônica depois de registrar seu número de telefone usando uma mensagem de texto. Os usuários podem resolver esse problema alterando seu método padrão seguindo as instruções no artigo [Gerenciar suas informações de segurança (visualização)](../user-help/security-info-manage-settings.md#change-your-info).

**Usuário não pode acessar a nova experiência de registro depois que um administrador desative o seu método de padrão**

   * Alguns usuários podem não conseguir acessar a nova experiência de registro se o administrador tiver desativado o método MFA padrão registrado anteriormente. Aqui está um exemplo de cenário:
      1. O usuário registrou previamente seu número de telefone e definiu seu método padrão como chamada telefônica.
      2. O administrador desativa a chamada telefônica como um método MFA para o locatário.
      3. O usuário é solicitado a se registrar durante o login porque precisa registrar um método adicional para atender à política SSPR do inquilino.
      4. Usuário tenta se registrar, mas não é possível acessar a página e fica preso em um loop porque um método padrão não está definido.

## <a name="next-steps"></a>Próximas etapas

[Saiba como implantar a redefinição de senha de autoatendimento do Azure AD](howto-sspr-deployment.md)

[Saiba como exigir a autenticação multifator para entrar](howto-mfa-getstarted.md)

[Saiba como configurar métodos de autenticação de usuário](https://aka.ms/securityinfoguide)
