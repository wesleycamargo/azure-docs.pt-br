---
title: Configurar informações de segurança para usar questões de segurança - Active Directory do Azure | Microsoft Docs
description: Configure suas informações de segurança para verificar sua identidade usando perguntas de segurança predefinidas.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: b4913d55ee3d254f197512d9251ae750d8896f53
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160431"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Configurar informações de segurança para usar perguntas de segurança predefinidas (visualização)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca tiver configurado suas informações de segurança, você será solicitado a fazê-lo agora.

## <a name="set-up-security-questions"></a>Configurar perguntas de segurança

Dependendo das configurações da sua organização, você pode ser solicitado a adicionar perguntas de segurança às suas informações de segurança ao fazer login. Caso contrário, para começar a configurar perguntas de segurança nas informações de segurança, siga as etapas em [gerenciar suas informações de segurança](security-info-manage-settings.md).

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas para as perguntas de outra pessoa.

>[!Note]
>As questões de segurança são armazenadas de maneira privada e segura em um objeto de usuário no diretório e só podem ser respondidas por você durante o registro. Não há como seu administrador ler ou modificar suas perguntas ou respostas.<br>Se você não vir a opção de perguntas de segurança, é possível que sua organização não permita que você use questões de segurança para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-choose-and-answer-your-security-questions"></a>Para escolher e responder às suas perguntas de segurança

1. Selecione **perguntas de segurança**e, em seguida, escolha quais questões de segurança que você deseja responder. 

    O número de questões de segurança que você precisa escolher é determinado pelo seu administrador.

    ![Página de informações de segurança, escolha suas perguntas de segurança](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Fornecer respostas para suas perguntas selecionadas e, em seguida, selecione **feito**.

## <a name="additional-security-info-options"></a>Opções de informações de segurança adicionais

Você tem opções para como contatos de sua organização a verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.