---
title: Visão geral do aplicativo Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o aplicativo Microsoft Authenticator, incluindo o que é, como ele funciona e quais informações estão incluídas nesta seção do conteúdo.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.openlocfilehash: 2379f1ff4fb4385015cc6077cb923cab998d1d11
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175206"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>O que é o aplicativo Microsoft Authenticator?
O aplicativo Microsoft Authenticator ajuda você a entrar em suas contas quando você usa a verificação de dois fatores. A verificação de dois fatores ajuda você a acessar suas contas com mais segurança, especialmente ao exibir informações confidenciais. Como as senhas podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é uma etapa adicional de segurança que ajuda a proteger sua conta, dificultando que ela seja invadida por outras pessoas.

Você pode usar o aplicativo Microsoft Authenticator de várias maneiras, incluindo:

- Fornecendo um prompt para um segundo método de verificação depois de entrar usando seu nome de usuário e a senha.

- Fornecendo a entrada sem exigir uma senha, usando seu nome de usuário e o dispositivo móvel com a impressão digital, o rosto ou o PIN.

>[!Important]
>Este conteúdo destina-se aos usuários. Se você for um administrador, é possível encontrar mais informações sobre como configurar e gerenciar seu ambiente do Azure Active Directory (Azure AD) na [Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory).<br><br>Caso esteja tendo problemas ao entrar em sua conta, confira [Quando você não consegue entrar em sua conta Microsoft](https://support.microsoft.com/help/12429) para obter ajuda.  Obtenha mais informações sobre o que fazer quando receber a mensagem [“Essa conta Microsoft não existe”](https://support.microsoft.com/help/13811) ao tentar entrar em sua conta Microsoft.

## <a name="terminology"></a>Terminologia
|Termo|DESCRIÇÃO|
|----|-----------|
|Verificação de dois fatores |Um processo de verificação que exige que você use especificamente a apenas duas partes das informações de verificação, como uma senha e um PIN. O aplicativo Microsoft Authenticator dá suporte tanto para verificação de dois fatores padrão quanto para entrada sem senha.|
|Autenticação Multifator (MFA)|Toda a verificação de dois fatores é uma autenticação multifator, exigindo que você use *pelo menos* duas partes das informações de verificação, dependendo dos requisitos da sua organização.|
|Conta da Microsoft (também chamada de MSA)|Você cria suas próprias contas pessoais para obter acesso aos serviços de nuvem e produtos da Microsoft voltados ao consumidor, como Outlook, OneDrive, Xbox LIVE ou Office 365. Sua conta da Microsoft é criada e armazenada no sistema de contas de identidade de consumidor da Microsoft executado pela Microsoft.|
|Conta corporativa ou de estudante|Sua organização cria sua conta corporativa ou de estudante (como alain@contoso.com) para permitir que você acesse recursos internos e potencialmente restritos, como Microsoft Azure, Windows Intune e Office 365.|

## <a name="how-two-factor-verification-works-with-the-app"></a>Como funciona a verificação de dois fatores com o aplicativo
A verificação de dois fatores funciona com o aplicativo Microsoft Authenticator das seguintes maneiras:

- **Notificação.** Digite seu nome de usuário e senha no dispositivo em que você está fazendo logon para sua conta corporativa ou de estudante ou sua conta Microsoft pessoal, então o aplicativo Microsoft Authenticator enviará uma notificação solicitando a você **Aprovar a entrada**. Escolher **aprovar** se reconhece a tentativa de logon. Caso contrário, escolha **Deny**. Se você escolher **Negar**, também poderá marcar a solicitação como fraudulenta.

- **Código de verificação.** Digite seu nome de usuário e senha no dispositivo em que você está fazendo logon na sua conta corporativa ou de estudante ou sua conta Microsoft pessoal e, em seguida, copie o código de verificação associado da tela **Contas** do aplicativo Microsoft Authenticator.

- **Entrada sem senha.** Digite seu nome de usuário no dispositivo no qual você está fazendo logon para sua conta corporativa ou de estudante ou conta Microsoft pessoal, depois use seu dispositivo móvel para verificar sua identidade usando sua impressão digital, rosto ou PIN. Para esse método, você não precisa inserir a senha.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Se deseja usar as funcionalidades biométricas do dispositivo
Se você usar um PIN para concluir o processo de autenticação, poderá configurar o aplicativo Microsoft Authenticator para, em vez disso, usar as funcionalidades de reconhecimento facial ou impressão digital (biometria) do seu dispositivo. Você pode configurá-lo na primeira vez em que usar o aplicativo autenticador para verificar sua conta, selecionando a opção para usar os recursos biométricos do dispositivo como identificação, em vez de seu PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?
Dependendo do tipo de conta, sua organização pode decidir o que você deve usar a verificação de dois fatores ou talvez você possa decidir por conta própria.

- **Conta corporativa ou de estudante.** Se você estiver usando uma conta corporativa ou de estudante (por exemplo, alain@contoso.com), ficará a critério da sua organização decidir se você deverá usar a verificação de dois fatores, juntamente com os métodos de verificação específicos. Para obter mais informações sobre a verificação multifator, confira [O que a Autenticação Multifator Azure significa para mim](multi-factor-authentication-end-user.md). Para obter mais informações sobre como configurar informações de segurança para usar o aplicativo Microsoft Authentication, confira [Configurar informações de segurança para usar um aplicativo autenticador (versão prévia)](security-info-setup-auth-app.md).

- **Conta Microsoft pessoal.** Você pode optar por configurar a verificação de dois fatores para suas contas Microsoft pessoais (por exemplo, alain@outlook.com).

- **Conta não Microsoft pessoal.** Você pode optar por configurar a verificação de dois fatores para suas contas não Microsoft pessoais (por exemplo, alain@gmail.com). Suas contas não Microsoft não podem usar o termo verificação de dois fatores, mas você deve ser capaz de localizar o recurso nas configurações de **Segurança** ou **Entrada**.

## <a name="in-this-section"></a>Nesta seção

|Artigo |DESCRIÇÃO |
|------|------------|
|[Baixar e instalar o aplicativo](microsoft-authenticator-app-how-to.md)|Descreve em que local e como obter e instalar o aplicativo Microsoft Authenticator para dispositivos que executam Android, iOS e Windows Phone.|
|[Adicionar suas contas corporativas ou de estudante](microsoft-authenticator-app-add-work-account.md)|Descreve como adicionar suas várias contas corporativas ou de estudante ao aplicativo Microsoft Authenticator.|
|[Adicionar suas contas pessoais](microsoft-authenticator-app-add-personal-account.md)|Descreve como adicionar suas contas Microsoft e não Microsoft pessoais ao aplicativo Microsoft Authenticator.|
|[Adicionar manualmente suas contas](microsoft-authenticator-app-add-account-manual.md)|Descreve como adicionar manualmente suas contas ao aplicativo Microsoft Authenticator, caso você não consiga ler o código QR fornecido.|
|[Entrar usando o aplicativo](microsoft-authenticator-app-phone-signin-faq.md)|Descreve como entrar em suas várias contas usando o aplicativo Microsoft Authenticator.|
|[Credenciais das contas de backup e recuperação](microsoft-authenticator-app-backup-and-recovery.md)| Fornece informações sobre como fazer backup e recuperar as credenciais da sua conta usando o aplicativo Microsoft Authenticator.|
|[Perguntas frequentes sobre o aplicativo Microsoft Authenticator](microsoft-authenticator-app-faq.md)|Fornece respostas para perguntas frequentes sobre o aplicativo.|
