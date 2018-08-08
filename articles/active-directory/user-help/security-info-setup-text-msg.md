---
title: Configurar informações de segurança para usar mensagens de texto - Active Directory do Azure | Microsoft Docs
description: Configure suas informações de segurança para verificar sua identidade usando uma mensagem de texto (SMS).
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
ms.openlocfilehash: f20bcb4245a2870d8eaa442c6f747ee533b362db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347568"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Configurar informações de segurança para usar mensagens de texto (visualização)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca configurou suas informações de segurança, será solicitado a fazer isso agora.

## <a name="set-up-text-messaging"></a>Configurar mensagens de texto

Dependendo das configurações da sua organização, você pode ser solicitado a adicionar mensagens de texto às suas informações de segurança quando fizer login. Caso contrário, para começar a configurar mensagens de texto em informações de segurança, siga as etapas em [Gerenciar suas informações de segurança](security-info-manage-settings.md).

A opção de mensagem de texto faz parte da opção de telefone, portanto, você configurará tudo da mesma maneira que faria para o seu número de telefone, mas, em vez de fazer com que a Microsoft ligue para você, optará por usar uma mensagem de texto. Se você não vir a opção de telefone, é possível que sua organização não permita que você use um número de telefone para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-use-a-text-message"></a>Para usar uma mensagem de texto

1. Selecione a opção **Phone**.

    O assistente **Configurar seu telefone** é exibido.

    ![Configure o seu código de país ou região e o número de telefone](media/security-info/security-info-keep-secure-setup-text.png)

2. Escolha seu **País ou Região** na caixa suspensa, digite seu número de telefone (incluindo o código de área, se aplicável) na caixa **Número de Telefone**, selecione o campo **Text me a código**, e depois selecione **Próximo**.

    Você receberá uma mensagem de texto com um código que precisará inserir em uma página de verificação.

    ![Página de verificação para inserir o código da mensagem de texto](media/security-info/security-info-keep-secure-verify-text-msg.png)

    Suas informações de segurança são atualizadas para enviar uma mensagem de texto para confirmar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha de autoatendimento.

    >[!Note]
    >Se você deseja receber uma chamada telefônica em vez de uma mensagem de texto, siga as etapas no artigo [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

## <a name="additional-security-info-options"></a>Opções de informações de segurança adicionais

Você tem opções de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Dispositivo móvel ou chamada telefônica de trabalho.** Digite o número do seu dispositivo móvel e receba uma ligação telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

- **Perguntas de segurança.** Responda a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção está disponível apenas para redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obtenha dicas de solução de problemas e ajude a problemas de login no artigo [Não é possível fazer login em sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).