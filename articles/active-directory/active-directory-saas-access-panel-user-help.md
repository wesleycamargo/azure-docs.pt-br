---
title: Obter ajuda para acessar e usar o portal Meus aplicativos no Azure Active Directory | Microsoft Docs
description: Obter ajuda para entrar e executar tarefas comuns no painel de acesso.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 285ee0c4e7f963c372c4f95face73864ba2a4f68
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Solucionar problemas de acesso e uso do portal Meus aplicativos

Caso esteja tendo problemas para entrar ou usar o portal Meus aplicativos, tente essas dicas de solução de problemas antes de entrar em contato com a assistência técnica ou com seu administrador para obter ajuda.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Estou com problemas para entrar no portal Meus Aplicativos

Tente essas dicas gerais:

- Primeiro, verifique se você está usando a URL correta, [https://myapps.microsoft.com](https://myapps.microsoft.com).
- Tente adicionar a URL aos sites confiáveis de seu navegador.
- Verifique se sua senha está correta e não expirou. Para obter mais informações, consulte [Redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md).
- Verifique se suas informações de contato de autenticação estão atualizadas e não estão bloqueando o acesso. Para obter mais informações, consulte [O que a Autenticação Multifator do Azure significa para mim?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Tente limpar os cookies do navegador e tente entrar novamente.

Se ainda estiver enfrentando problemas ao tentar entrar, entre em contato com seu administrador.


## <a name="i-seem-to-be-having-password-issues"></a>Pareço estar tendo problemas com a senha

Caso tenha esquecido sua senha, nunca tenha recebido uma de sua equipe de TI, sua conta tenha sido bloqueada ou queira alterar a senha, consulte [Preciso de ajuda, esqueci minha senha do Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Preciso me registrar para redefinição de senha

Você poderá redefinir sua senha ou desbloquear sua conta sem precisar falar com alguém usando a SSPR (redefinição de senha de autoatendimento). Antes de usar essa funcionalidade, você deve registrar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que seu administrador exige. Para obter mais informações, consulte [Registrar-se para redefinição de senha por autoatendimento](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Estou com problemas para instalar a Extensão de Entrada Segura dos Meus Aplicativos

O portal Meus aplicativos exige um navegador com suporte para JavaScript e com CSS habilitado. Se você estiver usando aplicativos de logon único baseado em senha, a extensão associada também precisará ser instalada. Essa extensão é baixada automaticamente quando você inicia um aplicativo configurado para aplicativos de logon único baseado em senha.

Verifique se você está cumprindo os seguintes requisitos de navegador:
- **Edge**: no Windows 10 Anniversary Edition ou posterior.
- **Chrome**: no Windows 7 ou posterior e no Mac OS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.
- **Internet Explorer 8, 9, 10 ou 11**: no Windows 7 ou posterior (suporte limitado).

Também é possível baixar a extensão para Chrome e Edge diretamente nos sites a seguir:

- [Extensão para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão para Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Se você instalou a extensão e ainda está enfrentando problemas, tente o seguinte:

- Verifique as configurações de extensão do navegador para ter certeza de que a extensão está habilitada.
- Reinicie seu navegador e entre no portal Meus aplicativos.
- Limpe os cookies do navegador e entre no portal Meus aplicativos.
- Para acessar uma ferramenta de diagnóstico e as instruções passo a passo sobre como configurar a extensão para o Internet Explorer, consulte [Solucionar problemas da Extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Usar a Extensão de Entrada Segura dos Meus Aplicativos
* Caso esteja usando uma URL dos Meus Aplicativos diferente de `https://myapps.microsoft.com`, configure sua URL padrão fazendo o seguinte:
   1. Enquanto *não* estiver conectado à extensão, clique com o botão direito do mouse no ícone da extensão.
   2. No menu, selecione **URL dos Meus aplicativos**.
   3. Selecione a URL padrão.
   4. Selecione o ícone da extensão.
   5. Para conectar-se à extensão, selecione **Entrar para começar**.

* Para entrar diretamente em um aplicativo a partir do navegador, faça o seguinte:
   1. Depois de instalar a extensão, entre nela selecionando **Entrar para começar**.
   2. Entre no aplicativo com a URL de logon.  
       A URL de logon geralmente é a URL do aplicativo que exibe o formulário de inscrição.
      A extensão deve alterar o estado e lhe informar que a senha está disponível.
   3. Para entrar, selecione o ícone da extensão.

* Para iniciar um aplicativo a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, entre nela selecionando **Entrar para começar**.
   2. Selecione o ícone da extensão para abrir o menu.
   3. Pesquise um aplicativo disponível no portal Meus aplicativos.
   4. Na lista de resultados de pesquisa, selecione o aplicativo.  
       Os três últimos aplicativos usados são exibidos na lista de atalhos **Usados Recentemente**.

> [!NOTE]
> Essas opções estão disponíveis apenas para Edge, Chrome e Firefox.

## <a name="how-do-i-add-a-new-app"></a>Como fazer para adicionar um novo aplicativo?

1.  Na página **Aplicativos**, selecione **Adicionar Aplicativo**.
2.  Procure o aplicativo que deseja adicionar e selecione **Adicionar**.

   > [!NOTE]
   > * Você só pode acessar essa opção se o administrador a tiver habilitado para sua conta.
   > * Se o aplicativo exigir permissão, talvez seja necessário aguardar a aprovação do administrador.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Como fazer para gerenciar as associações de grupo?

Selecione os bloco de **Grupos** e faça uma das seguinte ações: 
* Para criar um grupo, em **Grupos que possuo**, selecione **Criar grupo** e siga as instruções.
* Para entrar em um grupo, em **Grupos nos quais estou**, selecione **Entrar no grupo** e siga as instruções.

   > [!NOTE]
   > * Você só pode acessar essa opção se o administrador a tiver habilitado para sua conta.
   > * Caso seja membro de um grupo, você pode exibir detalhes e sair do grupo.
   > * Caso seja dono de um grupo, você pode exibir detalhes, adicionar ou remover membros e sair do grupo.
   >


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre solução de problemas, consulte [Problemas ao usar o site do painel de acesso do aplicativo ou o aplicativo móvel](active-directory-application-access-panel-content-map.md).

