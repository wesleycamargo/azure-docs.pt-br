---
title: 'Azure AD: redefinir sua senha | Microsoft Docs'
description: "Usar a redefinição de senha de autoatendimento para recuperar o acesso à sua conta"
services: active-directory
keywords: "Gerenciamento de senha do Active Directory, gerenciamento de senha, redefinição de senha de autoatendimento do Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: bb62d6973dff00932e72ddb4b2c344f070537828
ms.lasthandoff: 04/14/2017


---
# <a name="help-i-forgot-my-password"></a>Esqueci minha senha

Etapa 1... não entre em pânico

Se os cenários a seguir se aplicam a você, podemos ajudar

* Você não tem certeza de como acessar sua conta e não lembra da senha
* Uma senha não foi atribuída e seu administrador o mandou para cá

## <a name="unlock-my-account"></a>Desbloquear minha conta

Se você está aqui para desbloquear sua conta, siga as etapas abaixo. Quando você vir **Escolher uma nova senha** na etapa 6 abaixo, poderá desbloquear ou alterar sua senha e desbloqueá-la.

## <a name="reset-my-password"></a>Redefinir a minha senha

Para voltar à sua conta, siga as etapas abaixo.
1. Em qualquer página de entrada de conta corporativa ou de estudante, clique no link **Não consegue acessar sua conta?** e em **Conta corporativa ou de estudante** ou vá diretamente para a [página de redefinição de senha](https://passwordreset.microsoftonline.com/)

    ![Não é possível acessar sua conta?][Login]

2. Insira sua **ID de usuário** da conta corporativa ou de estudante e prove que você não é um robô passando no desafio CAPTCHA, inserindo o texto apresentado a você e clicando em **Avançar**

   > [!NOTE]
   > Se o administrador não habilitou essa funcionalidade, aparecerá um link "entre em contato com seu administrador" para que o administrador possa fornecer assistência via email ou um portal da Web.
   >

3. Dependendo de como o administrador configurou o sistema, você poderá ver uma ou mais das seguintes opções
    * **Enviar para meu email alternativo**: envia um email com um código de seis dígitos para seu email alternativo ou seu email de autenticação (você escolhe).
    * **Enviar SMS para meu celular**: envia uma mensagem com um código de seis dígitos para seu celular ou seu email de autenticação (você escolhe).
    * **Ligar para meu celular**: chama o seu celular ou o telefone de autenticação (você escolhe). Pressione a chave # para verificar a chamada.
    * **Ligar para meu telefone comercial**: liga para o telefone comercial. Pressione a chave # para verificar a chamada.
    * **Responder às perguntas de segurança**: exibe suas perguntas de segurança previamente registradas para que você possa respondê-las.
4. Preencha os campos obrigatórios com respostas para as informações de desafio e clique em **Avançar**

    ![Verificar os dados de autenticação][Verification]

5. O administrador pode exigir uma etapa de verificação adicional e você poderá ter que repetir a etapa 4 selecionando uma opção diferente
6. Na página **Escolher uma nova senha**, insira uma nova senha que atenda aos requisitos da sua organização, confirme sua escolha e clique em **Concluir**

    ![Alterar sua senha][Change]

7. Quando você vir **A senha do usuário foi redefinida.**, poderá entrar com sua nova senha.

    ![A senha do usuário foi redefinida][Complete]

Após usar esse método para desbloquear ou redefinir sua senha, você receberá um email confirmando que o processo foi concluído vindo de uma conta como "Microsoft em nome da sua organização aqui". Se você receber um email como este e não tiver feito redefinição de senha de autoatendimento para recuperar o acesso à sua conta, entre em contato com o administrador.

## <a name="change-my-password"></a>Alterar minha senha

Se você souber a senha e precisar alterá-la, experimente as etapas a seguir

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar sua senha no portal do Office 365

1. Clique em seu perfil no lado superior direito e clique em **Exibir conta**
2. **Segurança/Privacidade**
3. **Senha**
4. Insira sua senha antiga, defina e confirme sua nova senha
5. **Enviar**

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a sua senha no Painel de Acesso do Azure

1. Entre no [Portal de acesso do Azure](https://myapps.microsoft.com/) usando sua senha existente
2. Clique em seu perfil no canto superior direito e clique em **Perfil**
3. **Alterar senha**
4. Insira sua senha antiga, defina e confirme sua nova senha
5. **Enviar**

## <a name="next-steps"></a>Próximas etapas

* [Como registrar para usar a redefinição de senha de autoatendimento ](active-directory-passwords-reset-register.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de logon Não consegue acessar sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificar os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar sua senha"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A senha do usuário foi redefinida"

