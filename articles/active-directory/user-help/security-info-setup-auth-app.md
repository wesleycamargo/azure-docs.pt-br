---
title: Configurar informações de segurança para usar um aplicativo autenticador - Azure Active Directory | Microsoft Docs
description: Configure suas informações de segurança para verificar sua identidade usando o aplicativo Microsoft Authenticator.
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
ms.openlocfilehash: 191afec8733258519370d11942c0f6d18da6b603
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180391"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Configurar informações de segurança para usar um aplicativo autenticador (visualização)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

A configuração das informações de segurança exige que você faça login em sua conta do trabalho ou da escola e conclua o processo de registro. Se você nunca configurou suas informações de segurança, será solicitado a fazer isso agora.

## <a name="set-up-the-microsoft-authenticator-app"></a>Configurar o aplicativo Microsoft Authenticator

Dependendo das configurações da sua organização, você pode ser solicitado a configurar o aplicativo Microsoft Authenticator quando fizer login. Caso contrário, para começar a configurar o aplicativo Microsoft Authenticator em informações de segurança, siga as etapas em [Gerenciar suas informações de segurança](security-info-manage-settings.md).

Para baixar e saber mais sobre o aplicativo Microsoft Authenticator, consulte [Primeiros passos com o aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

>[!Note]
>Se você não quiser usar o aplicativo Microsoft Authenticator, poderá escolher um aplicativo diferente durante a configuração. Este artigo usa o aplicativo Microsoft Authenticator. Se você não vir a opção do aplicativo autenticador, é possível que sua organização não permita que você use um aplicativo de autenticação para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-use-the-microsoft-authenticator-app"></a>Para usar o aplicativo Microsoft Authenticator

1. Selecione o **aplicativo autenticador** opção.

    O assistente **Obter o aplicativo** é exibido.

    ![Obter o assistente do aplicativo, tela inicial](media/security-info/security-info-auth-app-wizard.png)

    Se você não quiser usar o aplicativo Microsoft Authenticator, clique no link **Desejo usar um aplicativo autenticador diferente** na tela **Obter o aplicativo**.

2. Depois de instalar o aplicativo Microsoft Authenticator, selecione **Próximo**.

    Se você receber uma solicitação, permita notificações, adicione uma nova conta e selecione **Conta do trabalho ou da escola**.

3. Selecione **Avançar**.

    A tela **Digitalizar o código QR** é exibida.

    ![Digitalize o código QR usando o aplicativo Autenticador](media/security-info/security-info-scan-qr.png)

4. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** da **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho ou conta de escola**. 

5. Se você tiver um aplicativo de leitura de código QR, escaneie o código fornecido. Se você não tiver um aplicativo de leitura de código, poderá selecionar **Não é possível verificar o link do código QR** e inserir manualmente o código e o URL no aplicativo Microsoft Authenticator.

6. Use o aplicativo Microsoft Authenticator para aprovar a notificação para ativar o aplicativo.

    Suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha de autoatendimento.

    >[!Note]
    >Se sua organização permitir, você também terá um código de verificação, juntamente com as notificações de aplicativo Microsoft Authenticator. Se você quiser tornar o código o seu método padrão, siga as instruções em [Gerenciar suas informações de segurança](security-info-setup-auth-app.md).

## <a name="additional-security-info-options"></a>Opções de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).
    
    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Se você precisar atualizar suas informações de segurança, siga as instruções no artigo [Gerenciar informações de segurança](security-info-manage-settings.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
