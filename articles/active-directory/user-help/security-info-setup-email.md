---
title: Configurar informações de segurança para usar email - Azure Active Directory | Microsoft Docs
description: Configure suas informações de segurança para confirmar sua identidade usando seu endereço de e-mail comercial ou da escola.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18124286d78520e8b89a61c5d12760c9ad784c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197208"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Configurar informações de segurança para usar o email (visualização)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca tiver configurado suas informações de segurança, você será solicitado a fazê-lo agora.

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

    As informações de segurança são atualizadas para usar o endereço de email e verificar a identidade ao usar a redefinição de senha.

## <a name="additional-security-info-options"></a>Opções de informações de segurança adicionais

Você tem opções para como contatos de sua organização a verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).
    
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
