---
title: Configurar informações de segurança para usar chamadas telefônicas - Active Directory do Azure | Microsoft Docs
description: Configure suas informações de segurança para confirmar sua identidade usando um dispositivo móvel ou um número de telefone comercial.
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
ms.openlocfilehash: 701a47a3907c77dbf5d51692e5d1ddc0c49ab985
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390095"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>Configurar informações de segurança para usar chamadas telefônicas (visualização)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca tiver configurado suas informações de segurança, você será solicitado a fazê-lo agora.

## <a name="set-up-phone-calls"></a>Configurar chamadas telefônicas

Dependendo das configurações da sua organização, você pode ser solicitado a adicionar um número de telefone às suas informações de segurança quando fizer login. Caso contrário, para começar a configurar chamadas telefônicas em informações de segurança, siga as etapas em [Gerenciar suas informações de segurança](security-info-manage-settings.md).

>[!Note]
>Informações de segurança não suportam o uso de extensões de telefone. Mesmo se você adicionar o formato adequado, +1 4255551234X12345, as extensões serão removidas antes da chamada ser feita.<br>Se você não vir a opção de telefone, é possível que sua organização não permita que você use chamadas telefônicas para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-use-your-phone-number"></a>Para usar seu número de telefonePara usar seu número de telefone

1. Selecione o **Phone** opção.

    O **configure o telefone** assistente é exibido.

    ![Configurar o seu número de telefone e o código do país ou região](media/security-info/security-info-keep-secure-setup-phone.png)

2. Escolher sua **país ou região** na caixa suspensa, digite seu número de telefone (incluindo código de área, se aplicável) para o **número de telefone** caixa, selecione o **ligar para mim** opção, e, em seguida, selecione **próxima**.

    Você receberá uma ligação para confirmar que digitou o número de telefone correto. Nesse momento, você será solicitado a pressionar a tecla (#) para confirmar e concluir sua configuração.

    ![Verifique a tela do seu telefone, mostrando que a chamada foi respondida com sucesso](media/security-info/security-info-keep-secure-verify-phone-call.png)

    Suas informações de segurança são atualizadas para usar seu número de telefone para confirmar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha de autoatendimento.

    >[!Note]
    >Se você deseja receber uma mensagem de texto em vez de uma chamada telefônica em seu dispositivo móvel, siga as etapas em [Configurar informações de segurança para usar o artigo de mensagens de texto (SMS)](security-info-setup-text-msg.md).

## <a name="additional-security-info-options"></a>Opções de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).
    
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.