---
title: "Implementação: Azure AD SSPR | Microsoft Docs"
description: "Dicas para uma distribuição bem-sucedida da redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Como distribuir com sucesso a redefinição de senha de autoatendimento

A maioria dos clientes segue as etapas abaixo para garantir uma distribuição tranquila da funcionalidade de SSPR.

1. [Habilitar a redefinição de senha em seu diretório](active-directory-passwords-getting-started.md).
2. [Configurar permissões do AD locais para write-back de senha](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Configurar o write-back de senha](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local.
4. [Atribuir e verificar as licenças necessárias](active-directory-passwords-licensing.md).
5. Se você quiser distribuir gradualmente a SSPR, você pode limitar o acesso a um grupo de usuários para que você possa fazer um teste com um grupo específico. Para isso, defina a caixa de ativar/desativar **Redefinição da senha de autoatendimento habilitada** como **Selecionada** e selecione um grupo de segurança para habilitar a redefinição de senha. 
6. Popular [Dados de autenticação](active-directory-passwords-data.md) para seus usuários como seu telefone comercial, telefone celular e endereço de email alternativo.
7. [Personalize a experiência de logon do Azure AD para incluir sua identidade visual da empresa](active-directory-passwords-customize.md).
8. Ensine seus usuários a usar a SSPR, enviando-os instruções de como registrar e como redefinir.
9. Você pode optar por impor o registro a qualquer momento e exigir que os usuários confirmem novamente as informações de autenticação após um período determinado.
10. Ao longo do tempo, revise os [relatórios fornecidos pelo Azure AD](active-directory-passwords-reporting.md) para examinar os usuários que usam e se registram.
11. Quando você estiver pronto, habilitar a redefinição de senha para todos os usuários, definir a caixa ativar/desativar **Redefinição de senha de autoatendimento habilitada** para **Todos**. 

   > [!IMPORTANT]
   > Teste a SSPR com um usuário e não como um administrador, uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso [artigo de política de senha](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Distribuição baseada em email

Muitos clientes acreditam que uma campanha de email com instruções simples de uso é a maneira mais fácil para que os usuários usem a SSPR. [Você criou três e-mails simples que você pode usar como modelos para ajudá-lo em sua distribuição.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Em breve** o modelo de email a ser usado em semanas ou dias antes da distribuição para que os usuários saibam que eles precisam fazer algo.
* **Disponível agora** modelo de email a ser usado no dia de início para orientar os usuários a se registrarem e confirmar seus dados de autenticação para que possam usar a SSPR quando necessário.
* **Lembrete de inscrição** modelo de email por alguns dias ou semanas após a implantação para lembrar aos usuários de se registrarem e confirmarem seus dados de autenticação.

![Email][Email]

## <a name="creating-your-own-password-portal"></a>Criando seu próprio portal de senha

Muitos de nossos clientes grandes preferem hospedar uma página e criar uma entrada DNS raiz, como https://passwords.contoso.com. Eles populam esta página com links para a redefinição da senha do Azure AD, registro da redefinição de senha, portais de alteração de senha e outras informações específicas da organização. Em comunicações de email ou folhetos enviados, você pode incluir um URL personalizada que seja fácil de lembrar para que os usuários possam acessar quando precisarem usar os serviços.

* Portal de redefinição de senha - https://aka.ms/sspr
* Portal de registro de redefinição de senha - http://aka.ms/ssprsetup
* Portal de alteração de senha - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Para o seu benefício criamos um exemplo de página, que você pode usar e personalizar de acordo com as necessidades de sua organização, que podem ser baixadas do [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Usando o registro imposto

Se desejar que os usuários se registrem para a redefinição de senha, você pode forçá-los a se registrar quando entrarem usando o Azure AD. Você pode habilitar essa opção na folha de **redefinição de senha** do seu diretório ativando a opção **Exigir que os Usuários se Registrem ao Entrar** na guia **Registro**.

Os administradores podem exigir que os usuários se registrem novamente após um período definindo o **número de dias antes que os usuários são solicitados a reconfirmar suas informações de autenticação** entre 0 e 730 dias.

Depois de habilitar essa opção, os usuários que entram verão uma mensagem informando-os que seu administrador solicitou que eles verifiquem suas informações de autenticação.

## <a name="populate-authentication-data"></a>Popular os dados de autenticação

Se você [popular os dados de autenticação para seus usuários](active-directory-passwords-data.md), os usuários não precisam se registrar para redefinição de senha antes de poderem usar a SSPR. Desde que os usuários tenham dados de autenticação definidos que atendam à política de redefinição de senha que você definiu, os usuários serão capazes de redefinir suas senhas.

## <a name="disabling-self-service-password-reset"></a>Desabilitação da redefinição da senha de autoatendimento

Desabilitar a redefinição de senha de autoatendimento é tão simples quanto abrir seu locatário do Azure AD e acessar **Redefinição de Senha**, **Propriedades** e escolher **Ninguém** em **Redefinição da Senha de Autoatendimento Habilitada**

## <a name="next-steps"></a>Próximas etapas

* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pela SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Personalizar esses modelos de email para se ajustar aos seus requisitos organizacionais"