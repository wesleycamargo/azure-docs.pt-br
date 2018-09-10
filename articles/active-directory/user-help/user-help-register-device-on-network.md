---
title: Registrar seu dispositivo pessoal na rede da sua organização - Azure Active Directory | Microsoft Docs
description: Saiba como registrar seu dispositivo pessoal na rede da sua organização para acessar os recursos protegidos da sua organização.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180394"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registre seu dispositivo pessoal na rede da sua organização
Registre seu dispositivo pessoal, geralmente um telefone ou tablet, na rede da sua organização. Depois que seu dispositivo for registrado, ele poderá acessar os recursos restritos da sua organização.

>[!Note]
>Este artigo usa um dispositivo Windows para fins de demonstração, mas você também pode registrar dispositivos executando iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>O que acontece quando você registra seu dispositivo
Enquanto você estiver registrando seu dispositivo na rede da sua organização, as seguintes ações ocorrerá:

- Windows registra seu dispositivo na rede da sua organização.

- Opcionalmente, com base nas escolhas da sua organização, pode ser solicitado que você configure a verificação em duas etapas por meio de [Autenticação de vários fatores](multi-factor-authentication-end-user-first-time.md) ou [informações de segurança](user-help-security-info-overview.md).

- Opcionalmente, com base nas opções da sua organização, você pode estar automaticamente inscrito no gerenciamento de dispositivos móveis, como o Microsoft Intune. Para obter mais informações sobre como se inscrever no Microsoft Intune, consulte [Inscrever seu dispositivo no Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Você irá percorrer o processo de entrada, usando o nome de usuário e a senha para sua conta pessoal da Microsoft.

## <a name="to-register-your-windows-device"></a>Para registrar seu dispositivo Windows

Siga estas etapas para registrar seu dispositivo pessoal em sua rede.

1. Abra **as configurações**e, em seguida, selecione **contas**.

    ![Contas na tela Configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Email e contas**e, em seguida, selecione **ingressar em uma conta da Microsoft**.

    ![Email e contas e adicionar links de conta da Microsoft](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. Sobre o **adicionar sua conta da Microsoft** tela, digite seu endereço de email para sua conta pessoal da Microsoft.

    ![Adicionar sua tela de conta da Microsoft, com o email](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. Sobre o **digite a senha** tela, digite a senha para sua conta pessoal da Microsoft e, em seguida, selecione **entrar**.

    ![Insira a tela de senha](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Conclua o restante do processo de registro, incluindo aprovar sua solicitação de verificação de identidade (se você usar a verificação em duas etapas) e configurar Windows Hello (se necessário).

## <a name="to-make-sure-youre-registered"></a>Para garantir que você está registrado
Você pode ter certeza de que você está registrado, observando as configurações.

1. Abra **as configurações**e, em seguida, selecione **contas**.

    ![Contas na tela Configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Email e contas**e verifique se você vir sua conta da Microsoft pessoal.

    ![Acessar tela de trabalho ou escola com conta contoso conectada](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>Próximas etapas
Depois de registrar seu dispositivo pessoal para a rede da sua organização, você poderá acessar a maioria de seus recursos.

- Se sua organização quer que você ingressar seu dispositivo de trabalho, consulte [ingressar seu dispositivo de trabalho para a rede da sua organização](user-help-join-device-on-network.md).



