---
title: Como aplicativos aparecem no painel de acesso | Microsoft Docs
description: "Solucionar problemas relacionados a por que um aplicativo está aparecendo no Painel de Acesso"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewr: japere
ms.openlocfilehash: 25e8bd404bc5b622568b8885f241c743f82f59cc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="how-applications-appear-on-the-access-panel"></a>Como aplicativos aparecem no painel de acesso

O Painel de Acesso é um portal baseado na Web que permite a um usuário com uma conta corporativa ou de estudante no Microsoft Azure AD (Azure Active Directory) exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure Active Directory concedeu acesso. Esses aplicativos são configurados em nome do usuário no portal do Azure AD. O administrador pode provisionar o aplicativo diretamente para o usuário ou para um grupo de que o usuário faz parte, o que faz com que o aplicativo apareça no Painel de Acesso do usuário.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se um aplicativo foi removido de um usuário ou grupo do qual o usuário é membro, tente entrar e sair novamente no Painel de Acesso do usuário após alguns minutos para ver se o aplicativo foi removido.

-   Se uma licença foi removida de um usuário ou grupo do qual o usuário é um membro, isso pode demorar dependendo do tamanho e da complexidade do grupo para as alterações a serem feitas. Espere mais algum tempo antes de entrar no Painel de Acesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados à atribuição de aplicativos para usuários

Um usuário pode estar vendo um aplicativo em seu Painel de Acesso por ter sido atribuído a ele anteriormente. A seguir, algumas maneiras de verificação:

-   [Verificar se um usuário está atribuído ao aplicativo](#check-if-a-user-is-assigned-to-the-application)

-   [Verificar se um usuário está sob uma licença relacionada ao aplicativo](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Verificar se um usuário está atribuído ao aplicativo

Para verificar se um usuário é atribuído ao aplicativo, execute estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory**, clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

6.  **Pesquise** pelo nome do aplicativo em questão.

7.  Clique em **Usuários e grupos**.

8.  Verifique se o usuário está atribuído ao aplicativo.

  * Se quiser remover o usuário do aplicativo, **clique na linha** do usuário e selecione **excluir**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verificar se um usuário está sob uma licença relacionada ao aplicativo

Para verificar as licenças atribuídas de um usuário, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory**, clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

   * Se o usuário for atribuído a uma licença do Office, isso permitirá que os aplicativos primários do Office apareçam no Painel de Acesso do usuário.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados à atribuição de aplicativos para usuários

Um usuário pode estar vendo um aplicativo em seu Painel de Acesso por fazer parte de um grupo ao qual o aplicativo foi atribuído. A seguir, algumas maneiras de verificação:

-   [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

-   [Verificar se um usuário é membro de um grupo atribuído a uma licença](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Verificar as associações de grupo de um usuário

Para verificar as associações de um grupo, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory**, clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Grupos.**

8.  Verifique se o usuário faz parte de um Grupo atribuído ao aplicativo.

   * Se quiser remover o usuário do grupo, **clique na linha** do grupo e selecione excluir.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Verificar se um usuário é membro de um grupo atribuído a uma licença

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory**, clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Usuários e grupos** no menu de navegação.

5.  Clique em **Todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Grupos.**

8.  Clique na linha de um grupo específico.

9.  Clique em **Licenças** para ver quais licenças o grupo atribuiu a ele.

  * Se o grupo for atribuído a uma licença do Office, isso poderá permitir que determinados aplicativos primários do Office apareçam no Painel de Acesso do usuário.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email do usuário)

-   ID do locatário

-   Tipo de navegador

-   Fuso horário e hora/cronograma durante o erro

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Gerenciando aplicativos com o Azure Active Directory](active-directory-enable-sso-scenario.md)
