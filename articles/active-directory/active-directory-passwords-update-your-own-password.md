---
title: 'Azure AD: redefinir sua senha | Microsoft Docs'
description: "Usar a redefinição de senha de autoatendimento para recuperar o acesso à conta de usuário corporativa ou de estudante"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 9595b318b8bd974f778dc12b38cccd949a4e7a1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="reset-your-work-or-school-password"></a>Redefinir sua senha corporativa ou de estudante

Se você tiver esquecido sua senha, nunca tiver recebido uma da sua equipe de suporte da empresa, tiver sua conta bloqueada, ou desejar alterar a senha, podemos ajudar. Se você souber a senha e somente precisar alterá-la, continue até a seção [Alterar minha senha](#change-my-password) abaixo.

   > [!NOTE]
   > Se você estiver tentando retornar a sua conta pessoal, como Xbox, hotmail.com ou outlook.com, tente as [sugestões encontradas neste artigo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Redefinir ou desbloquear minha senha para uma conta corporativa ou de estudante

Caso não consiga acessar sua conta por causa de um dos seguintes:

* Sua senha não está funcionando e você deseja restaurá-la.
* Você sabe sua senha, mas a conta está bloqueada e você deseja desbloqueá-la.

Siga as etapas abaixo para acessar a redefinição de senha de autoatendimento do Azure AD ou SSPR como gostamos de chamá-lo e volte para sua conta.

1. Em qualquer página de entrada de conta corporativa ou de estudante, clique no link **Não consegue acessar sua conta?** e, e seguida, clique em **Conta corporativa ou de estudante** ou vá diretamente para a [página de redefinição de senha](https://passwordreset.microsoftonline.com/).

    ![Não é possível acessar sua conta?][Login]

2. Insira sua **ID de usuário** corporativo ou de estudante, para provar que você não é um robô inserindo os caracteres que você vê na tela, depois clique em **Avançar**.

   > [!NOTE]
   > Se sua equipe de TI não tiver habilitado essa funcionalidade, um link "entre em contato com seu administrador" será exibido para que sua equipe de TI possa ajudar, por email ou por um portal da Web próprio.
   >
   > Se você precisar desbloquear sua conta, neste momento escolha a opção "Sei minha senha, mas ainda não consigo entrar".
   >

3. Dependendo de como a equipe de TI configurou a SSPR, você poderá ver um ou mais dos seguintes métodos de autenticação. Você ou sua equipe de TI preencheu algumas dessas informações antes de usar o artigo [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).

   * **Enviar por email para o meu email alternativo**
   * **Enviar mensagem de texto para o meu celular**
   * **Ligar para o meu celular**
   * **Ligar para o meu telefone comercial**
   * **Responder minhas perguntas de segurança**

   Escolha uma opção, forneça as respostas corretas e clique em **Avançar**.

   ![Verificar os dados de autenticação][Verification]

4. A sua equipe de TI pode precisar de outras verificações e pode ser que você tenha que repetir a etapa 3 com uma opção diferente.
5. Na página **Escolher uma nova senha**, insira uma nova senha, confirme sua senha e clique em **Concluir**. Sua senha corporativa ou de estudante pode exigir certos requisitos; sugerimos que sua senha tenha de 8 a 16 caracteres com letras maiúsculas e minúsculas, números e caracteres especiais.
6. Quando você vir **A sua senha foi redefinida.**, você poderá entrar com sua nova senha.

    ![A senha do usuário foi redefinida][Complete]

Agora você deve ser capaz de acessar sua conta, caso contrário, entre em contato com a equipe de TI da sua organização para obter ajuda adicional.

Você pode receber um email de confirmação que vem de uma conta como "Microsoft em nome da \<sua organização>". Se você receber um email como este e você não tiver feito uma redefinição de senha de autoatendimento para recuperar o acesso à sua conta, entre em contato com a equipe de TI da sua organização.

## <a name="change-my-password"></a>Alterar minha senha

Se você souber a senha e precisar alterá-la, siga as etapas a seguir para alterar a sua senha.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar sua senha no portal do Office 365

Use este método se você normalmente acessa seus aplicativos usando o portal do Office

1. Entre na sua [conta do Office 365](https://www.office.com) usando sua senha existente
2. Clique em seu perfil no lado superior direito e clique em **Exibir conta**
3. Clique em **Segurança e privacidade** > **Senha**
4. Insira sua senha antiga, defina e confirme sua nova senha e clique em **Enviar**

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a sua senha no Painel de Acesso do Azure

Use este método se você normalmente acessa seus aplicativos usando o Portal de Acesso do Azure

1. Entre no [Portal de acesso do Azure](https://myapps.microsoft.com/) usando sua senha existente
2. Clique em seu perfil no canto superior direito e clique em **Perfil**
3. Clique em **Alterar Senha**
4. Insira sua senha antiga, defina e confirme sua nova senha e clique em **Enviar**

## <a name="reset-password-at-login"></a>Redefinir a senha no Logon

Se o seu administrador tiver habilitado essa funcionalidade, você poderá ver um link para **Redefinir senha** na sua tela de logon do Windows 10 Fall Creators Update.

![LoginScreen][LoginScreen]

Clicar no link **Redefinir senha** fará com que abra a experiência SSPR na tela de logon para que você possa redefinir a sua senha sem ter que fazer logon para acessar a experiência normal baseada na web.

1. Confirme o seu ID de Usuário e clique em **Próximo**
2. Escolha e confirme um método de contato para a verificação. A sua equipe de TI pode precisar de outras verificações, e pode ser que você tenha que repetir esta etapa novamente com uma opção diferente.

   ![ContactMethod][ContactMethod]

3. Na página **Criar uma nova senha**, insira uma nova senha, confirme-a e clique em **Próximo**. Sugerimos que sua senha tenha de 8 a 16 caracteres com letras maiúsculas e minúsculas, números e caracteres especiais.

   ![ResetPassword][ResetPassword]

4. Quando você vir **Sua senha foi redefinida**, clique em **Concluir**

Agora você deve ser capaz de acessar sua conta, caso contrário, entre em contato com a equipe de TI da sua organização para obter ajuda adicional.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Caso de erro| Que erro você vê?| Solução |
| --- | --- | --- |
| Uma página “Contate seu administrador” é exibida após a inserção da minha ID de usuário | Entre em contato com o seu administrador <br> <br> Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente. <br> <br> Você precisa entrar em contato com sua equipe de TI para receber mais assistência. | Você está vendo esta mensagem porque a equipe de TI gerencia sua senha no ambiente local e não permite que você redefina sua senha usando o link Não consegue acessar sua conta? <br> <br> Para redefinir sua senha, contate a equipe de TI diretamente para obter ajuda e informe que deseja redefinir sua senha, para que ela possa habilitar o recurso para você.|
| Recebo uma mensagem de erro "sua conta não está habilitada para redefinição de senha" depois de inserir a ID de usuário | Sua conta não está habilitada para redefinição de senha <br> <br> A equipe de TI não configurou sua conta para usar esse serviço. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a equipe de TI não habilitou a redefinição de senha usando o link Não consegue acessar sua conta? para sua organização ou não licenciou o uso por você. <br> <br> Para redefinir sua senha, clique no link “Contate um administrador” para enviar um email à equipe de TI da empresa e informe que deseja redefinir sua senha para que ela possa habilitar o recurso para você. |
| Recebo uma mensagem de erro "não foi possível verificar sua conta" depois de inserir a ID de usuário | Não foi possível verificar sua conta <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a redefinição de senha está habilitada, mas você não se registrou para usar o serviço. Para se registrar para redefinição de senha, vá para http://aka.ms/ssprsetup depois que você tiver recuperado o acesso à sua conta. <br> <br> A fim de redefinir sua senha, clique no link contate um administrador para enviar um email à equipe de TI da empresa. |

## <a name="next-steps"></a>Próximas etapas

* [Como registrar para usar a redefinição de senha de autoatendimento ](active-directory-passwords-reset-register.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de logon Não consegue acessar sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificar os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar sua senha"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A senha do usuário foi redefinida"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Link de redefinição de senha na tela de Logon do Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Verificar os dados de autenticação"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Alterar sua senha"
