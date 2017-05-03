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
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c95f52f028cdfaed35821275d816a24035ff02fb
ms.lasthandoff: 04/26/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>Ajuda de Esqueci minha senha do Azure AD

Se você esquecer sua senha, nunca recebeu uma da sua equipe de TI, teve sua conta bloqueada, ou deseja alterar a senha, podemos ajudar.

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Redefinir ou desbloquear minha senha para uma conta corporativa ou de estudante

Para entrar na sua conta corporativa ou de estudante, siga as etapas abaixo para acessar a redefinição de senha de autoatendimento do Azure AD ou SSPR como gostamos de chamá-lo.

1. Em qualquer página de entrada de conta corporativa ou de estudante, clique no link **Não consegue acessar sua conta?** e, e seguida, clique em **Conta corporativa ou de estudante** ou vá diretamente para a [página de redefinição de senha](https://passwordreset.microsoftonline.com/).

   > [!NOTE]
   > Se você está tentando retornar a uma conta Pessoal como hotmail.com ou outlook.com tente as [sugestões encontradas neste artigo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >
    ![Não é possível acessar sua conta?][Login]

2. Insira sua **ID de usuário** corporativo ou de estudante, para provar que você não é um robô inserindo os caracteres que você vê na tela, depois clique em **Avançar**.

   > [!NOTE]
   > Se sua equipe de TI não tiver habilitado essa funcionalidade, um link "entre em contato com seu administrador" será exibido para que sua equipe de TI possa ajudar, por email ou por um portal da Web próprio.
   >

3. Dependendo de como a equipe de TI configurou o SSPR, você poderá ver uma ou mais das seguintes opções. Você ou sua equipe de TI preencheu algumas dessas informações antes de usar as informações [aqui](active-directory-passwords-reset-register.md).
    * **Enviar por email para o meu email alternativo**
    * **Enviar mensagem de texto para o meu celular**
    * **Ligar para o meu celular**
    * **Ligar para o meu telefone comercial**
    * **Responder minhas perguntas de segurança**

    Escolha uma opção, forneça as respostas corretas e clique em **Avançar**.

    ![Verificar os dados de autenticação][Verification]

4. A sua equipe de TI pode precisar de outras verificações e pode ser que você tenha que repetir a etapa 3 com uma opção diferente.
5. Na página **Escolher uma nova senha**, insira uma nova senha, confirme sua senha e clique em **Concluir**. Sugerimos que sua senha tenha de 8 a 16 caracteres com letras maiúsculas e minúsculas, números e caracteres especiais.

   > [!NOTE]
   > Se você precisar desbloquear sua conta, escolha a opção desbloquear apenas, ou alterar sua senha e desbloquear.
   >

6. Quando você vir **A sua senha foi redefinida.**, você poderá entrar com sua nova senha.

    ![A senha do usuário foi redefinida][Complete]

Agora você deve ser capaz de acessar sua conta, caso contrário, entre em contato com a equipe de TI da sua organização para obter ajuda adicional.

Você pode receber um email de confirmação que vem de uma conta como "Microsoft em nome da \<sua organização >\". Se você receber um email como este e você não tiver feito uma redefinição de senha de autoatendimento para recuperar o acesso à sua conta, entre em contato com a equipe de TI da sua organização.

## <a name="change-my-password"></a>Alterar minha senha

Se você souber a senha e precisar alterá-la, siga as etapas a seguir para alterar a sua senha.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar sua senha no portal do Office 365

Use este método se você normalmente acessa seus aplicativos usando o portal do Office

1. Entre na sua [conta do Office 365](https://www.office.com)
2. Clique em seu perfil no lado superior direito e clique em **Exibir conta**
3. Clique em **Segurança e privacidade** > **Senha**
4. Insira sua senha antiga, defina e confirme sua nova senha e clique em **Enviar**

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a sua senha no Painel de Acesso do Azure

Use este método se você normalmente acessa seus aplicativos usando o Portal de Acesso do Azure

1. Entre no [Portal de acesso do Azure](https://myapps.microsoft.com/) usando sua senha existente
2. Clique em seu perfil no canto superior direito e clique em **Perfil**
3. Clique em **Alterar Senha**
4. Insira sua senha antiga, defina e confirme sua nova senha e clique em **Enviar**

## <a name="next-steps"></a>Próximas etapas

* [Como registrar para usar a redefinição de senha de autoatendimento ](active-directory-passwords-reset-register.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de logon Não consegue acessar sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificar os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar sua senha"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A senha do usuário foi redefinida"

