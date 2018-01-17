---
title: "Guia de implantação de redefinição de senha de autoatendimento - Azure Active Directory"
description: "Dicas para uma distribuição bem-sucedida da redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 4eb3465ec36b6e25a1a36fb5b15a823adf536d83
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Como distribuir com sucesso a redefinição de senha de autoatendimento

Para garantir uma distribuição uniforme da funcionalidade de autoatendimento de redefinição de senha (SSPR) do Azure Active Directory (Azure AD), a maioria dos clientes conclui as seguintes etapas:

1. [Habilitar a redefinição de senha em seu diretório](active-directory-passwords-getting-started.md).
2. [Configurar permissões do Active Directory locais para write-back de senha](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurar o write-back de senha](active-directory-passwords-writeback.md#configure-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local.
4. [Atribuir e verificar as licenças necessárias](active-directory-passwords-licensing.md).
5. Determine se deseja fazer uma distribuição gradual. Se deseja distribuir o SSPR gradualmente, você pode limitar o acesso a um grupo de usuários para que você possa fazer um teste do programa com um grupo específico. Para fazer a distribuição para um grupo específico, defina a opção **Autoatendimento de Redefinição de Senha Habilitado** como **Selecionado** e selecione o grupo de segurança que deseja que seja capaz de usar a redefinição de senha. 
6. Preencha os [dados de autenticação](active-directory-passwords-data.md) necessários para que seus usuários se registrem, como telefone comercial, telefone celular e endereço de email alternativo.
7. [Personalize a experiência de logon do Azure AD para incluir sua identidade visual da empresa](active-directory-passwords-customize.md).
8. Ensine os usuários a usar o SSPR. Envie instruções para mostrar a eles como registrar e redefinir suas senhas.
9. Determine quando você deseja impor o registro. Você pode optar por impor o registro a qualquer momento. Você também pode exigir que os usuários confirmem novamente as informações de autenticação após um período determinado.
10. Usar o recurso de geração de relatórios. Ao longo do tempo, você pode examinar o registro de usuários e o uso com o [recurso de geração de relatórios que o Azure AD fornece](active-directory-passwords-reporting.md).
11. Habilitar a redefinição de senha. Quando você estiver pronto, habilite a redefinição de senha para todos os usuários definindo a opção **Autoatendimento Redefinição de Senha Habilitado** como **Todos**. 

   > [!NOTE]
   > Alterar essa opção de um grupo selecionado para qualquer pessoa não invalida os dados de autenticação existentes que um usuário registrou como parte de um grupo de teste. Usuários que estão configurados e têm dados de autenticação registrados válidos continuam a funcionar.

12. [Permitir que os usuários do Windows 10 redefinam sua senha na tela de logon](active-directory-passwords-login.md).

   > [!IMPORTANT]
   > Teste o SSPR com um usuário e não como um administrador, uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso artigo de [política de senha](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Distribuição baseada em email

Muitos clientes acham que a maneira mais fácil de fazer com que os usuários usem o SSPR é por meio de uma campanha de email que inclua instruções que sejam fáceis de seguir. [Criamos três emails simples que você pode usar como modelos para ajudá-lo em sua distribuição](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16):

* **Em breve**: um modelo de email que você usa nas semanas ou nos dias antes da distribuição para permitir que os usuários saibam que eles precisam fazer algo.
* **Disponível agora**: um modelo de email que você usa no dia do lançamento do programa para orientar os usuários a se registrarem e confirmarem seus dados de autenticação. Se os usuários se registrarem agora, eles terão o SSPR disponível quando precisarem.
* **Lembrete de inscrição**: um modelo de email para alguns dias ou algumas semanas após a implantação para lembrar aos usuários de se registrarem e confirmarem seus dados de autenticação.

![Email][Email]

## <a name="create-your-own-password-portal"></a>Criar seu próprio portal de senha

Muitos clientes preferem hospedar uma página e criar uma entrada DNS raiz, como https://passwords.contoso.com. Eles preenchem a página com links para as seguintes informações:

* [Portal de redefinição de senha do Azure AD - https://aka.ms/sspr](https://aka.ms/sspr)
* [Portal de registro de redefinição de senha do Azure AD - http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* [Portal de alteração de senha do Azure AD - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Outras informações específicas da organização

Em quaisquer comunicações por email ou folhetos enviados por você, é possível incluir uma URL personalizada que seja fácil de lembrar para que os usuários possam acessar quando precisarem usar os serviços. Para o seu benefício, criamos uma [página de exemplo de redefinição de senha](https://github.com/ajamess/password-reset-page) que pode ser usada e personalizada de acordo com as necessidades de sua organização.

## <a name="use-enforced-registration"></a>Usar o registro imposto

Se deseja que os usuários se registrem para a redefinição de senha, você pode exigir que eles se registrem quando entrarem usando o Azure AD. Você pode habilitar essa opção no painel de **Redefinição de senha** do seu diretório ativando a opção **Exigir que os Usuários se Registrem ao Entrar** na guia **Registro**.

Os administradores podem exigir que os usuários se registrem novamente após um período de tempo específico. Eles podem definir a opção **Número de dias antes que os usuários sejam solicitados a confirmar novamente suas informações de autenticação** de 0 a 730 dias.

Depois de habilitar essa opção, quando os usuários entrarem, eles verão uma mensagem dizendo que seu administrador solicitou que eles verifiquem as informações de autenticação.

## <a name="populate-authentication-data"></a>Popular os dados de autenticação

Você deve [preencher os dados de autenticação de seus usuários](active-directory-passwords-data.md). Dessa forma, os usuários não precisam se registrar para redefinição de senha antes de serem capazes de usar o SSPR. Desde que os usuários tenham fornecido dados de autenticação que atendam à política de redefinição de senha que você definiu, eles serão capazes de redefinir suas senhas.

## <a name="disable-self-service-password-reset"></a>Desabilitar a redefinição de senha por autoatendimento

É fácil desabilitar a redefinição de senha por autoatendimento. Abra o seu locatário do Azure AD e vá para **Redefinição de senha** > **Propriedades** e, em seguida, selecione **Nenhum** em **Redefinição de senha do serviço de autoatendimento habilitada**.

## <a name="next-steps"></a>Próximas etapas

* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalizar esses modelos de email para se ajustar aos seus requisitos organizacionais"
