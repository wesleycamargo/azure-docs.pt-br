---
title: Configurar informações de segurança para usar email - Azure Active Directory | Microsoft Docs
description: Configure suas informações de segurança para confirmar sua identidade usando seu endereço de e-mail comercial ou da escola.
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
ms.openlocfilehash: 34696802735fd2a145cd6383dcdc7e4a4b6368e2
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391402"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Configurar informações de segurança para usar o email (visualização)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca configurou suas informações de segurança, será solicitado a fazer isso agora.

## <a name="set-up-email"></a>Configurar email

Dependendo das configurações da sua organização, você pode ser solicitado a adicionar um endereço de e-mail às suas informações de segurança ao fazer login. Caso contrário, para começar a configurar o email em informações de segurança, siga as etapas em [Gerenciar suas informações de segurança](security-info-manage-settings.md).

>[!Note]
>Recomendamos o uso de uma conta de e-mail que não requeira sua senha de rede para acessar.<br>Se você não vir a opção de e-mail, é possível que sua organização não permita que você use um e-mail para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-use-your-email-address"></a>Para usar seu endereço de e-mail

1. Selecione a opção **E-mail** e digite seu endereço de e-mail na caixa. Este endereço de e-mail não pode ser seu e-mail corporativo ou escolar.

     ![Página de informações de segurança, com caixa de entrada de e-mail](media/security-info/security-info-keep-secure-setup-email.png)

2. Verifique se há um e-mail da Microsoft para sua organização, digite o código de verificação incluído na caixa **Verificar seu e-mail** e selecione **Concluído**.

     ![Página de informações de segurança, com caixa de entrada de código de verificação de e-mail](media/security-info/security-info-verify-email.png)

    >[!Note]
    >Se você não vir um e-mail da Microsoft em nome da sua organização, certifique-se de ter digitado seu endereço de e-mail corretamente e, em seguida, verifique as pastas Lixo Eletrônico ou Spam.

3. Na página **Manter sua conta segura**, selecione **Concluído**.

    Suas informações de segurança é atualizada para usar seu endereço de e-mail para verificar sua identidade ao usar a verificação em duas etapas.

## <a name="additional-security-info-options"></a>Opções adicionais de informação de segurança

Você tem opções de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira seu número de dispositivo móvel e receba um texto em um código que será usada para verificação em duas etapas ou a senha de redefinição. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [configurar informações de segurança para usar a mensagem de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).
    
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obtenha dicas de solução de problemas e ajude a problemas de login no artigo [Não é possível fazer login em sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).