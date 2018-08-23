---
title: Introdução ao aplicativo Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Saiba como atualizar para a versão mais recente do Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: c8e14091d7a3fb5b925735824b1dd3ce26f034e8
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42145470"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introdução ao aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator ajuda a impedir o acesso não autorizado a contas ea interromper transações fraudulentas, oferecendo um nível adicional de segurança para sua conta do trabalho ou da escola (por exemplo, alain@contoso.com) ou sua conta pessoal da Microsoft (por exemplo, alain@outlook.com) .

Ao usar o aplicativo para verificação em duas etapas, ele pode funcionar de duas maneiras:

- **Notificação.** O aplicativo envia uma notificação para seu dispositivo. Verifique se a notificação é à direita e, em seguida, selecione **Verify**. Se você não reconhecer a notificação, selecione **Negar**.

- **Código de verificação.** Depois de digitar seu nome de usuário e senha, você pode abrir o aplicativo e copiar o código de verificação fornecido na tela **Contas** na tela de login. O código de verificação atua como uma segunda forma de autenticação.

## <a name="opt-in-for-two-step-verification"></a>Aceitar a verificação em duas etapas

Sua organização decide se você usa a verificação em duas etapas com sua conta do trabalho ou da escola. Seu administrador informará quais métodos de verificação precisam ser configurados e usados. Para obter mais informações, consulte [O que a Autenticação de Vários Fator do Azure significa para mim](multi-factor-authentication-end-user.md).

Para sua conta pessoal da Microsoft, você pode configurar a verificação em duas etapas para você mesmo. Para informações e instruções mais detalhadas, consulte [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Você também pode usar o aplicativo Microsoft Authenticator com contas que não são da Microsoft. Essas contas podem chamar o elemento de algo diferente de verificação em duas etapas, mas você deve encontrá-lo nas configurações de segurança ou de login. Para obter mais informações sobre como configurar essas contas que não são da Microsoft, consulte os [vídeos de suporte ao cliente da Microsoft](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Instalar o aplicativo

O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). Para obter a melhor experiência, você deve permitir que o aplicativo receba notificações quando for solicitado. 

## <a name="add-accounts-to-the-app"></a>Adicionar contas ao aplicativo

Você pode adicionar contas de trabalho ou escola ou contas pessoais ao aplicativo Microsoft Authenticator. 

### <a name="add-a-personal-microsoft-account"></a>Adicionar uma conta pessoal da Microsoft

Para ter uma conta pessoal da Microsoft (aquela que você usa para entrar no Outlook.com, Xbox, Skype etc.), basta entrar em sua conta no aplicativo Microsoft Authenticator.

### <a name="add-a-work-or-school-account"></a>Adicionar uma conta corporativa ou de estudante

1. Se possível, vá para a tela [Verificação adicional de segurança](http://aka.ms/mfasetup) em outro PC ou dispositivo. Para obter informações sobre como chegar a essa tela, consulte [Alterando suas configurações de segurança](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) ou entre em contato com seu administrador.

    >[!Note]
    >Se o administrador ativou a experiência de visualização das informações de segurança, siga as instruções na seção [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

2. Marque a caixa ao lado de **aplicativo do autenticador** e selecione **Configurar**.

    ![Botão Configurar na tela de configurações da verificação de segurança](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    A tela **Configurar aplicativo móvel** é exibida com um código QR para você digitalizar com o aplicativo autenticador.

    ![Tela que fornece o código QR](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator. Na tela **Contas**, selecione **Adicionar conta** e, em seguida, selecione **Conta de trabalho ou escola**.

4. Use a câmera do seu dispositivo para escanear o código QR e, em seguida, selecione **Pronto** para fechar a tela do código QR.

    >[!Note]
    >Se sua câmera não estiver funcionando corretamente, você poderá [inserir o código QR e o URL manualmente](#add-an-account-to-the-app-manually).

    A tela **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que você use o mesmo código duas vezes.  

    ![Tela de contas](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Adicionar manualmente uma conta ao aplicativo

1. Vá para o **verificação de segurança adicional** tela. Para obter mais informações sobre como acessar essa tela, consulte [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Marque a caixa ao lado de **aplicativo do autenticador** e selecione **Configurar**.

    O **aplicativo móvel de configurar** tela é exibida.

3. Copie as informações de código e URL da tela **Configurar aplicativo móvel** para digitá-las manualmente no scanner QR.

4. Abra o aplicativo Microsoft Authenticator. Na tela **Contas**, selecione **Adicionar conta** e, em seguida, selecione **Conta de trabalho ou escola**.

5. Na tela de scanner de QR, selecione **insere código manualmente**.

    ![Tela para verificar um código QR](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Digite o código e o URL da tela com o código QR na tela **Adicionar uma conta** e, em seguida, selecione **Concluir**.

    ![Tela para inserir código e a URL](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    A tela **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que você use o mesmo código duas vezes.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Usando os recursos de impressão digital ou reconhecimento facial do seu dispositivo

Sua organização pode exigir um PIN para concluir sua verificação de identidade. Você pode configurar o aplicativo Microsoft Authenticator para usar os recursos de reconhecimento de impressão digital ou facial do seu dispositivo em vez de um PIN. Você pode configurá-lo na primeira vez em que usar o aplicativo autenticador para verificar sua conta, selecionando a opção para usar os recursos biométricos do dispositivo como identificação, em vez de seu PIN.

## <a name="use-the-app-when-you-sign-in"></a>Usar o aplicativo ao entrar

Depois de adicionar suas contas ao aplicativo, você pode usar o aplicativo para fazer login em suas contas.

Se você optar por usar códigos de verificação no aplicativo, começará a vê-los na página **Contas**. Os códigos mudam a cada 30 segundos para que você sempre tenha um novo código quando precisar de um. Mas você não precisa fazer nada com eles até que você entre e seja solicitado que você insira um código de verificação.

## <a name="next-steps"></a>Próximas etapas

- Se você tiver mais dúvidas gerais sobre o aplicativo, consulte [Perguntas frequentes sobre o Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- Se você quiser mais informações sobre a verificação em duas etapas, consulte [Configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se você quiser mais informações sobre informações de segurança, consulte [Gerenciar suas informações de segurança](security-info-manage-settings.md)
