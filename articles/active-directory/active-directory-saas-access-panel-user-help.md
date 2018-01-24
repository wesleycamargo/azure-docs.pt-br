---
title: Precisa de ajuda com o portal Meus Aplicativos no Azure Active Directory? | Microsoft Docs
description: "Obtenha instruções sobre como executar tarefas comuns ao trabalhar com o painel de acesso."
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
ms.openlocfilehash: 9bec51e1d49308baecc76143ec80902d2da418e8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Precisa de ajuda com o portal Meus Aplicativos?

Provavelmente, você chegou a esta página porque infelizmente se deparou com um problema ao usar o portal Meus Aplicativos. Embora haja casos em que é necessário entrar em contato com a assistência técnica ou com seu administrador para que um problema seja resolvido, temos aqui alguns tópicos de solução de problemas que poderão ajudá-lo primeiro.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Estou com problemas para entrar no portal Meus Aplicativos

Problemas gerais a serem verificados:

- Verifique se você está entrando na URL correta: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Tente adicionar a URL aos sites confiáveis de seu navegador.

- Verifique se sua senha não está expirada ou foi esquecida. Veja [aqui](active-directory-passwords-update-your-own-password.md) mais detalhes de como atualizar sua senha.

- Verifique se suas informações de contato de autenticação estão atualizadas e não estão bloqueando o acesso. Veja [aqui](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) mais detalhes de como configurar suas informações de autenticação.

- Tente limpar os cookies do navegador e tente entrar novamente.

Se ainda tiver problemas ao tentar entrar, entre em contato com seu administrador para obter ajuda.


## <a name="how-do-i-update-my-password"></a>Como fazer para atualizar minha senha?

Caso você tenha esquecido sua senha, nunca tenha recebido uma de sua equipe de TI, sua conta tenha sido bloqueada ou você queira alterar a senha, consulte [Preciso de ajuda, esqueci minha senha do Azure AD](active-directory-passwords-update-your-own-password.md) para obter mais detalhes.

## <a name="how-do-i-register-for-password-reset"></a>Como fazer para registrar-me para a redefinição de senha?

Como um usuário final, você poderá redefinir sua senha ou desbloquear sua conta sem precisar falar com uma pessoa usando a SSPR (redefinição de senha de autoatendimento). Antes de usar essa funcionalidade, você precisa registrar métodos de autenticação ou confirmar os métodos de autenticação predefinidos que administrador preencheu. Para obter mais detalhes, consulte [Registrar-se para redefinição de senha de por autoatendimento](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Estou com problemas para instalar a Extensão de Entrada Segura dos Meus Aplicativos

Verifique se você está atendendo aos requisitos de navegador:

- O portal exige um navegador com suporte para JavaScript e com CSS habilitado. Se você estiver usando aplicativos de logon único baseado em senha, a extensão associada também precisará ser instalada. Essa extensão é baixada automaticamente quando você inicia um aplicativo configurado para aplicativos de logon único baseado em senha.

- Os requisitos de navegador da extensão são:
    - Edge no Windows 10 Anniversary Edition ou posterior
    - Chrome no Windows 7 ou posterior e no MacOS X ou posterior
    - Firefox 26.0 ou posterior no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior
    - Internet Explorer 8, 9, 10 ou 11 no Windows 7 ou posterior (suporte limitado)

Também é possível baixar a extensão para Chrome e Edge diretamente pelos links abaixo:

- [Extensão para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Extensão para Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Após a instalação, tente executar estas etapas se houver problemas:

- Verifique nas configurações de extensão do navegador se a extensão está habilitada.

- Reinicie seu navegador e entre no portal Meus Aplicativos.

- Limpe os cookies do navegador e entre no portal Meus Aplicativos.
- Siga o guia [Solucionar problemas da Extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) para acessar uma ferramenta de diagnóstico e as instruções passo a passo sobre como configurar a extensão para o IE.

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>Como uso a Extensão de Entrada Segura dos Meus Aplicativos?
Alterar a URL padrão de Meus Aplicativos para a extensão

Se você estiver usando uma URL de Meus Aplicativos diferente de https://myapps.microsoft.com, será necessário configurar a URL padrão seguindo estas etapas:
1. Enquanto não tiver se conectado à extensão, **clique com o botão direito do mouse** no ícone da extensão.
2. Clique em **Selecionar URL de Meus Aplicativos** no menu.
3. **Selecione** a URL padrão.
4. Clique no ícone da extensão.
5. Conecte-se à extensão selecionando **Entre para começar**.

Entrar diretamente em um aplicativo do navegador
1. Depois de instalar a extensão, conecte-se a ela selecionando **Entre para começar**.
2. Navegue até a **URL de logon** do aplicativo no qual você deseja entrar, ela geralmente é a URL do aplicativo que exibe o formulário de logon.
3. O estado da extensão deve se alterar e informar que uma senha está disponível, clique então no **ícone da extensão** para entrar

Iniciar um aplicativo da extensão
1. Depois de instalar a extensão, conecte-se a ela selecionando **Entre para começar**.
2. Clique no ícone da extensão para abrir o menu.
3. **Pesquise** por um aplicativo disponível no portal Meus Aplicativos.
4. Clique no aplicativo de **resultados da pesquisa** para iniciá-lo.
5. Os três últimos aplicativos iniciados também aparecerão na lista de atalhos **Usados Recentemente**

> [!NOTE]
> Essas opções só estão disponíveis para o Edge, Chrome e Firefox.

## <a name="how-do-i-add-a-new-app"></a>Como fazer para adicionar um novo aplicativo?

1.  Na página **Aplicativos**, clique em **Adicionar Aplicativo**.

2.  Pesquise pelo aplicativo que deseja adicionar e clique em **Adicionar**.

**Comentários:**

- Você terá acesso a essa opção apenas se seu administrador a tiver habilitado para sua conta.

- Se o aplicativo exigir uma permissão, talvez seja necessário aguardar a aprovação do administrador.


## <a name="how-do-i-manage-my-group-memberships"></a>Como fazer para gerenciar as associações de grupo?

1. Clique no bloco **Grupos**. 
2. Para criar um grupo, em Grupos que possuo, clique em **Criar grupo** e siga as instruções.
3. Para entrar em um grupo, em Grupos nos quais estou, clique em **Entrar no grupo** e siga as instruções.

**Comentários:**

- Você terá acesso a essa opção apenas se seu administrador a tiver habilitado para sua conta.

- Os grupos de que você é membro permitem exibir detalhes e sair do grupo.

- Os grupos de que você é proprietário permitem exibir detalhes, adicionar ou remover membros e sair do grupo.


## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas de informações relacionadas, consulte [Problemas ao usar o site do painel de acesso do aplicativo ou o aplicativo móvel](active-directory-application-access-panel-content-map.md)

