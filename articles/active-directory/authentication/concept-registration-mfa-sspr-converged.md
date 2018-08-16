---
title: Registro convergido para o Microsoft Azure Active Directory SSPR e o MFA (visualização pública)
description: Autenticação de vários fatores do Microsoft Azure Active Directory e registro de redefinição de senha de autoatendimento (visualização pública)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: d6915ce659d96021d4185be3818919fcfb9d4371
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492885"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>Registro convergente para redefinição de senha de autoatendimento e Autenticação de vários fatores do Azure (visualização pública)

Até agora, os usuários precisavam registrar métodos de autenticação para a Autenticação Multifatores do Azure (MFA) e a redefinição de senha de autoatendimento (SSPR) em dois portais diferentes. Muitos usuários ficaram confusos com o fato de que métodos similares foram usados tanto para o Azure MFA quanto para o SSPR e não se registrariam em ambos os portais. Essa disparidade fez com que alguns usuários não pudessem usar o Azure MFA ou SSPR quando necessário, levando a uma chamada de assistência técnica e, potencialmente, um usuário chateado. Agora, os usuários podem se registrar uma vez e obter os benefícios do Azure MFA e SSPR, eliminando a necessidade de registrar seus métodos de autenticação para esses recursos duas vezes.  

Antes de ativar essa nova experiência para sua organização, recomendamos que você analise este artigo, bem como nossa [documentação do usuário final](https://aka.ms/securityinfoguide) para entender o impacto que a experiência terá em seus usuários. Você pode usar um [relatórios do usuário final](https://aka.ms/securityinfoguide) para treinar e preparar seus usuários para uma nova experiência e garantir um lançamento bem-sucedido.

|     |
| --- |
| O registro convergente para redefinição de senha de autoatendimento e a Autenticação de vários fatores do Azure é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Para permitir que seus usuários registrem métodos de autenticação para a Autenticação de Vários Fator do Azure e a redefinição de senha de autoatendimento em uma única experiência, conclua as etapas a seguir:

1. Entre no portal do Azure como administrador global ou administrador de usuários.
2. Navegue até **Active Directory do Azure**, **Configurações do usuário**, **Gerenciar configurações para os recursos de visualização do painel de acesso**.
3. Em os **usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança**. Você pode optar por ativar um grupo de usuários **Selecionado** ou para **todos** os usuários.

Os usuários podem agora ir para [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) para registrar seus métodos de autenticação para MFA e SSPR. Para saber mais sobre o que seus usuários verão nessa nova experiência, consulte nossa [documentação do usuário final](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Depois que você ativar essa experiência, os usuários que se registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência terão a capacidade de usá-los para MFA e SSPR, se esses métodos estiverem ativados nas políticas MFA e SSPR. Se você desabilitar essa experiência, os usuários que navegarem para a página anterior de registro do SSPR em aka.ms/ssprsetup serão obrigados a executar o MFA antes de poderem acessar a página.  

## <a name="how-it-works"></a>Como ele funciona

Se um usuário já tiver registrado métodos de autenticação por meio das experiências de registro MFA e SSPR separadas, ele não precisará registrar essas informações novamente. No entanto, se suas configurações exigirem que os usuários se registrem no MFA ou no SSPR, poderão ver um aviso para revisar suas informações de segurança ao fazer login.

Se um usuário tiver registrado um método que possa ser usado para o MFA, ele será solicitado a realizar o MFA antes de poder acessar a nova experiência.

Se você aplicou o registro para MFA ou SSPR e um usuário ainda não se registrou, ele será solicitado a se registrar quando fizer login.

Os usuários que são solicitados a registrar-se ao fazer login verão a seguinte experiência:

![Registro convergido. Configurar os métodos como um novo usuário](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Essa experiência só será mostrada quando um usuário for solicitado a se registrar enquanto estiver fazendo login. Os usuários que acessarem diretamente a nova experiência em aka.ms/setupsecurityinfo verão uma versão diferente da experiência, descrita posteriormente neste artigo.

Os métodos de autenticação mostrados serão alterados com base nos métodos ativados nas políticas do MFA ou do SSPR. O usuário será solicitado a registrar o número mínimo de métodos de autenticação necessários para estar em conformidade com a política de MFA, a política de SSPR ou ambos. Se houver flexibilidade em quais métodos de autenticação o usuário pode registrar, eles poderão selecionar **Escolher informações de segurança** para escolher outros métodos de autenticação.  

Ao contrário da experiência anterior de registro do MFA, os usuários não serão solicitados a registrar uma senha do aplicativo quando passarem pela nova experiência de registro. Em vez disso, eles devem seguir as etapas listadas em nosso tutorial de senhas de aplicativos para registrar senhas de aplicativos na nova experiência.  

Depois que um usuário concluir o registro, o método MFA padrão será definido automaticamente. Se o usuário registrou um aplicativo autenticador, o método padrão será definido como app. Se o usuário não registrou um aplicativo autenticador e registrou apenas seu número de telefone, o método padrão será definido como chamada telefônica. Os usuários podem alterar sua padrão acessando [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) e selecionando **alterar padrão**.  

Se o registro não for aplicado, os usuários poderão gerenciar seus próprios métodos de autenticação em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Se um usuário tiver registrado anteriormente um método que possa ser usado para realizar o MFA, ele será solicitado a realizar o MFA antes de poder acessar a página.  

![Registro convergido. Editar métodos como um usuário registrado](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

Nesta página, os usuários verão métodos de autenticação e métodos de autenticação registrados anteriormente para eles, como o Office phone. Os usuários também podem adicionar, editar ou excluir seus métodos de autenticação (exceto o telefone do Office).  

Registros de auditoria para essa nova experiência existem na categoria Métodos de Autenticação do log de auditoria.  

## <a name="known-issues"></a>Problemas conhecidos

**O método MFA padrão é definido como chamada telefônica quando o usuário registra o telefone usando uma mensagem de texto**

   * Alguns usuários podem perceber que o método MFA padrão é definido como chamada telefônica depois de registrar seu número de telefone usando uma mensagem de texto. Os usuários podem resolver esse problema alterando o método padrão, seguindo as instruções encontradas no artigo [Gerenciar suas informações de segurança (visualização)](../user-help/security-info-manage-settings.md#change-your-info).

**Usuário incapaz de acessar a nova experiência de registro depois que o administrador desabilita seu método padrão**

   * Alguns usuários podem não conseguir acessar a nova experiência de registro se o método MFA padrão registrado anteriormente tiver sido desabilitado pelo administrador. Aqui está um exemplo de cenário:
      1. O usuário registrou previamente seu número de telefone e definiu seu método padrão como chamada telefônica.
      2. O administrador desativa a chamada telefônica como um método MFA para o locatário.
      3. O usuário é solicitado a se registrar durante o login porque precisa registrar um método adicional para atender à política SSPR do inquilino.
      4. O usuário tenta se registrar, mas não pode acessar a página devido a não ter um conjunto de métodos padrão e está preso em um loop.

## <a name="next-steps"></a>Próximas etapas

[Saiba como implantar a redefinição de senha de autoatendimento do Azure AD](howto-sspr-deployment.md)

[Saiba como exigir autenticação com vários fatores ao fazer login](howto-mfa-getstarted.md)

[Documentação de configuração do método de autenticação do usuário final](https://aka.ms/securityinfoguide)