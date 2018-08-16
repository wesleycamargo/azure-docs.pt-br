---
title: Telefone do Microsoft Authenticator-entrar - Azure Active Directory | Microsoft Docs
description: Fornece informações detalhadas e instruções passo a passo sobre como usar seu telefone para fazer login no seu trabalho, na escola e nas contas pessoais da Microsoft, em vez de digitar sua senha.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: a6df90a1ae1dddbf7f76a7ef6c7e13d9e645dd9c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528418"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Entre com seu telefone, não com sua senha

O aplicativo Microsoft Authenticator não só ajuda a manter suas contas seguras executando a verificação em duas etapas depois que você insere sua senha, mas agora pode substituir a senha da sua conta da Microsoft por completo.

Esse recurso está disponível no iOS e Android dispositivos que executam o Android 6.0 ou superior.

## <a name="how-it-works"></a>Como ele funciona

Muitos de vocês usam o aplicativo Microsoft Authenticator para verificação em duas etapas quando você entrar em sua conta da Microsoft. Você digita sua senha e, em seguida, acessa o aplicativo para aprovar uma notificação ou para obter um código de verificação. Com o login por telefone, você pula a senha e faz toda a verificação de identidade no seu telefone. Como o login por telefone é um tipo de verificação em duas etapas, você ainda precisa confirmar sua identidade fornecendo uma coisa que você sabe e algo que você tem. O telefone ainda é o que você tem, mas o PIN do seu telefone ou a chave biométrica agora é o que você sabe.

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>Ativar o login por telefone para sua conta do trabalho ou da escola

Se a sua organização usa o Azure Active Directory para gerenciamento e autenticação de identidades, você pode usar o logon de telefone depois de ativá-lo no aplicativo Microsoft Authenticator. 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>Para ativar o telefone entrar para sua conta

- **Se você tiver este ícone![ de login no telefone ](./media/microsoft-authenticator-app-phone-signin-faq/icon.png)em seu telefone.** Você já configurou entrada pelo telefone no. Você pode ser solicitado a fazer upgrade da sua conta para adicionar notificações por push, para ser notificado sobre solicitações de autenticação fora do aplicativo.

- **Se você já usa o aplicativo Microsoft Authenticator para a verificação em duas etapas.** Vá para a tela **Contas** do aplicativo, selecione a seta suspensa da sua conta do trabalho ou da escola e, em seguida, selecione **Ativar login pelo telefone**.

- **Se você usa o aplicativo Microsoft Authenticator para sua conta pessoal, mas não para sua conta do trabalho ou da escola.** Siga as instruções de verificação em duas etapas da sua organização e, em seguida, ativar o telefone entrar. Para mais informações, consulte [Introdução ao aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md)

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>Para usar o login por telefone para fazer login na sua conta do trabalho ou da escola

1.  Entre em sua conta do trabalho ou da escola, como de costume.

    Depois de digitar seu nome de usuário, você verá uma página com um número de 2 dígitos, pedindo que você aprove o login por meio do aplicativo Microsoft Authenticator. Se você não quiser usar esse método de login, selecione **Use sua senha em vez disso** e faça login usando sua senha.

    ![Página de entrada](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. No aplicativo de autenticação da Microsoft, você receberá uma notificação solicitando que **aprove o login**. Escolher **aprovar** se reconhece a tentativa de logon. Caso contrário, escolha **Deny**.

3. Toque no mesmo número que você vê na tela **Aprovar login**. Use o PIN do seu telefone ou sua chave biométrica para concluir a autenticação.
 
    ![Aprovar página de login](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>Ativar o login por telefone para sua conta pessoal

Você pode ativar a entrada de telefone para sua conta pessoal da Microsoft, como a conta que você usa para entrar no Outlook.com, Xbox ou Skype.

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>Para ativar o login por telefone para sua conta pessoal

- **Se você não usar o aplicativo Microsoft Authenticator.** Baixe e instale o aplicativo Microsoft Authenticator e adicione sua conta pessoal da Microsoft fazendo login na conta na tela **Adicionar contas** do aplicativo Microsoft Authenticator. Para obter mais informações, consulte [começar com o aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md).
As novas contas são ativadas automaticamente para que você possa começar a usar o login por telefone imediatamente.

- **Se você já usa o aplicativo Microsoft Authenticator para a verificação em duas etapas.** Vá para a tela **Contas** do aplicativo, selecione a seta suspensa da sua conta do trabalho ou da escola e, em seguida, selecione **Ativar login pelo telefone**.

>[!NOTE]
>Para ajudar a proteger sua conta, o aplicativo exige um PIN ou um bloqueio biométrico no seu dispositivo. Se você mantiver seu smartphone desbloqueado, o aplicativo exigirá que você configure uma trava de segurança antes de ativar o login por telefone.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>Para usar o login por telefone para fazer login na sua conta pessoal

1.  Vá para a página de login da conta pessoal da Microsoft e, em vez de digitar sua senha, selecione o link **Usar um aplicativo Microsoft Authenticator em vez disso**.

    A Microsoft envia uma notificação para seu telefone. 

2.  Aprove a notificação.

## <a name="phone-sign-in-faq"></a>FAQ de entrada do telefone

|**Pergunta**|**Solução**|
|--------------|-------------|
|**Como o login com meu telefone é mais seguro do que digitar uma senha?**|Hoje em dia a maioria das pessoas entra em sites ou aplicativos usando um nome de usuário e senha. Infelizmente, as senhas podem ser perdidas, roubadas ou adivinhadas por hackers.<br><br>Depois de configurar o aplicativo Microsoft Authenticator, ele cria uma chave em seu telefone para desbloquear sua conta, protegida pelo PIN ou pelo bloqueio biométrico de seu telefone. Essa chave é usada, em seguida, para provar sua identidade ao entrar.<br><br>**Importante**<br>Seus dados são usados apenas para proteger sua chave localmente. Nunca é enviado ou armazenado na nuvem.|
|**Isso substitui a verificação em duas etapas? Eu deverá desativá-lo?**| Estamos trabalhando para expandir o escopo do login por telefone, mas, por enquanto, ainda há lugares no ecossistema da Microsoft que não o suportam. Nesses locais, verificação em duas etapas ainda está sendo usada para entrada segura. Por esse motivo, não, você não deve desativar a verificação em duas etapas para a sua conta.|
|**Se eu mantiver a verificação em duas etapas ativada para minha conta, tenho que aprovar duas notificações?**| Não. Fazer login na sua conta da Microsoft usando seu telefone também é considerado uma verificação em duas etapas, portanto, não é necessária a segunda aprovação.|
|**E se eu perder meu telefone ou não tiver comigo? Como posso acessar minha conta?**| Você sempre pode selecionar o link Usar uma senha em vez disso na página de login para voltar a usar sua senha. No entanto, se você usar a verificação em duas etapas, ainda precisará usar um segundo método para confirmar sua identidade.<br><br>**Importante**<br>Você é incentivado a ter mais de um método de verificação atualizado associado à sua conta.<br><br>Você pode gerenciar seus métodos de verificação para contas pessoais na [página Configurações de segurança](https://account.live.com/proofs/manage). Para contas do trabalho ou da escola, você pode acessar a página [Verificação adicional de segurança](https://aka.ms/MFASetup) da sua organização ou a página **Manter sua conta segura**, se o administrador tiver ativado informações de segurança. Para mais informações sobre informações de segurança, consulte [Gerenciar suas informações de segurança](security-info-manage-settings.md).<br><br>Se você não conseguir gerenciar seus métodos de verificação, deverá entrar em contato com seu administrador.|
|**Como eu paro de usar esse recurso e volto a usar minha senha?**|Para contas pessoais, selecione o link **Usar uma senha em vez disso** durante o login. Sua escolha mais recente é lembrada e oferecida por padrão na próxima vez que você fizer login. Se você quiser voltar a usar o login por telefone, selecione o link **Usar um aplicativo em vez disso** durante o login.<br><br>Para contas do trabalho ou da escola, você deve remover a conta abrindo o aplicativo Microsoft Authenticator, selecionando o menu **Editar contas** e, em seguida, excluindo sua conta.|
|**Por que não posso usar mais de uma conta do trabalho ou da escola para fazer login no telefone?**| Um telefone deve estar registrado em uma única conta de trabalho ou escola. Se você quiser ativar o login por telefone para uma conta de trabalho ou escola diferente, primeiro cancele o registro da conta antiga na página **Configurações**.|
|**Posso entrar no meu PC usando o meu telefone?**| Para o seu PC, é recomendável entrar usando o Windows Hello no Windows 10. O Windows Hello permite que você use seu rosto, impressão digital ou PIN para fazer login.|
|**Pode usar entrada pelo telefone com meu Windows Phone?**| Não. Este recurso não é compatível com o aplicativo Microsoft Authenticator no Windows Phone.|

## <a name="next-steps"></a>Próximas etapas

-   Se você tiver mais dúvidas gerais sobre o aplicativo, consulte [Perguntas frequentes sobre o Microsoft Authenticator](microsoft-authenticator-app-faq.md)

-   Se você quiser mais informações sobre a verificação em duas etapas, consulte [Configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

-   Se você quiser mais informações sobre informações de segurança, consulte [Gerenciar suas informações de segurança](security-info-manage-settings.md)
