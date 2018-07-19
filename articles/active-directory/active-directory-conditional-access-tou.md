---
title: Guia de início rápido - exigem termos de uso para ser aceito antes de acessar a aplicativos de nuvem que são protegidos pelo acesso condicional do Azure Active Directory | Microsoft Docs
description: Neste início rápido, você aprenderá como você pode exigir que os termos de uso são aceitos antes de conceder acesso aos aplicativos de nuvem selecionado pelo acesso condicional do Azure Active Directory.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional, termos de uso
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d5fad320c49691626c820429b07b6864137760b9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448697"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Guia de início rápido: Exigem termos de uso para ser aceito antes de acessar a aplicativos de nuvem 

Antes de acessar determinados aplicativos de nuvem em seu ambiente, talvez você queira obter o consentimento dos usuários na forma de aceitar os termos de uso (ToU). Acesso condicional do Azure AD (Active Directory do Azure) oferece: 

- Um método simples para configurar os termos de uso
- A opção de exigir a aceitar os termos de uso por meio de uma política de acesso condicional  

Este início rápido mostra como configurar uma [política de acesso condicional do Azure AD](active-directory-conditional-access-azure-portal.md) que requer um termo de uso ser aceito para um aplicativo de nuvem selecionado em seu ambiente.

![Criar política](./media/active-directory-conditional-access-tou/5555.png)


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="prerequisites"></a>pré-requisitos 

Para concluir o cenário deste início rápido, você precisa de:

- **Acesso a uma edição do Azure AD Premium** – o acesso condicional ao Azure AD é um recurso do Azure AD Premium. 

- **Uma conta de teste chamada Isabella Simonsen** – caso não saiba como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Teste seu logon

O objetivo desta etapa é obter uma impressão da experiência de logon sem uma política de acesso condicional.

**Teste seu logon:**

1. Entre no [Portal do Azure](https://portal.azure.com/) como Isabella Simonsen.

2. Saia.




## <a name="create-your-terms-of-use"></a>Criar seus termos de uso

Esta seção fornece as etapas para criar um exemplo de termos de uso. Quando você cria um termos de uso, você seleciona um valor para **impor com modelos de política de acesso condicional**. Selecionando **política personalizada** abre a caixa de diálogo para criar uma nova política de acesso condicional, assim que seus termos de uso foi criado.


**Para criar seus termos de uso:**

1. No Microsoft Word, crie um novo documento.

2. Digite **meu termos de uso**e, em seguida, salve o documento no seu computador como **mytou.pdf**.

3. Faça login no [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

4. No portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-tou/02.png)

5. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-tou/03.png) 

6. Na seção **Gerenciar**, clique em **Termos de uso**.

    ![Termos de uso](./media/active-directory-conditional-access-tou/04.png) 

7. No menu na parte superior, clique em **Novos termos**.

    ![Termos de uso](./media/active-directory-conditional-access-tou/05.png) 

8. Na página **Novos termos de uso**:

    ![Termos de uso](./media/active-directory-conditional-access-tou/112.png) 

    a. Na caixa de texto **Nome**, digite **Meus Termos de uso**.

    b. Na caixa de texto **Exibir nome**, digite **Meus Termos de uso**.

    c. Carregue seus termos de usar o arquivo de PDF.

    d. Como **Linguagem**, selecione **Inglês**.

    e. Para **Exigir que os usuários expandam os Termos de uso**, selecione **Ativado**.

    f. Para **Impor com modelos da política de acesso condicional**, selecione **Políticas personalizadas**.

    g. Clique em **Criar**.
 


## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional 

Esta seção mostra como criar a política de acesso condicional necessária. O cenário deste início rápido usa:

- O portal do Azure como espaço reservado para um aplicativo de nuvem que requer seus termos de uso serem aceitos. 
- Seu usuário de exemplo para testar a política de acesso condicional.  

Em sua política, defina:

|Configuração |Valor|
|---     | --- |
|Usuários e grupos | Isabella Simonsen |
|Aplicativos na nuvem | Gerenciamento do Microsoft Azure |
|Conceder acesso | Meus termos de uso |
 

![Criar política](./media/active-directory-conditional-access-tou/1234.png)

 


**Para configurar sua política de acesso condicional:**

1. Na página **Novo**, na caixa de texto **Nome**, digite **Exige Termos de uso para Isabella**.

    ![NOME](./media/active-directory-conditional-access-tou/71.png)

2. Na seção **Atribuição**, clique em **Usuários e Grupos**.

    ![Usuários e grupos](./media/active-directory-conditional-access-tou/06.png)

3. Na página **Usuários e grupos**:

    ![Usuários e grupos](./media/active-directory-conditional-access-tou/24.png)

    a. Clique em **Selecionar usuários e grupos** e selecione **Usuários e grupos**.

    b. Clique em **Selecionar**.

    c. Na página **Selecionar**, selecione **Isabella Simonsen** e depois clique em **Selecionar**.

    d. Na página **Usuários e grupos**, clique em **Concluído**.

4. Clique em **Aplicativos de nuvem**.

    ![Aplicativos na nuvem](./media/active-directory-conditional-access-tou/08.png)

5. Na página de **aplicativos de nuvem**:

    ![Selecionar aplicativos de nuvem](./media/active-directory-conditional-access-tou/26.png)

    a. Clique em **Selecionar aplicativos**.

    b. Clique em **Selecionar**.

    c. Na página **Selecionar**, selecione **Microsoft Azure** e clique em **Selecionar**.

    d. Na página **Aplicativos de nuvem**, clique em **Concluído**.


6. Na seção **Controles de acesso**, clique em **Conceder**.

    ![Controles de acesso](./media/active-directory-conditional-access-tou/10.png)

7. Na página **Grant**:

    ![Concessão](./media/active-directory-conditional-access-tou/111.png)

    a. Selecione **Conceder acesso**.

    a. Selecione **Meus Termos de uso**.

    b. Clique em **Selecionar**.

8. Na seção **Habilitar política**, clique em **Ativar**.

    ![Habilitar política](./media/active-directory-conditional-access-tou/18.png)

9. Clique em **Criar**.


## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou a política de acesso condicional, provavelmente deseja saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política “What If” do acesso condicional para simular uma entrada de seu usuário de teste. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação.  

Para inicializar a ferramenta de avaliação de política “What If”, defina:

- **Isabella Simonsen** como usuário 
- **Gerenciamento do Microsoft Azure** como aplicativo de nuvem


Ao clicar em **What If**, é criado um relatório de simulação que mostra:

- **Exigir termos de uso para Isabella** sob **políticas que se aplicam** 
- **Meus Termos de uso** como **Conceder Controles**.

![Ferramenta de política “What If”](./media/active-directory-conditional-access-tou/79.png)



**Para avaliar sua política de acesso condicional:**

1. Na página [Acesso condicional – Políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), no menu superior, clique em **What If**.  
 
    ![What If](./media/active-directory-conditional-access-tou/14.png)

2. Clique **Usuários**, selecione **Isabella Simonsen** e depois clique em **Selecionar**.

    ![Usuário](./media/active-directory-conditional-access-tou/15.png)

2. Para selecionar um aplicativo de nuvem:

    ![Aplicativos na nuvem](./media/active-directory-conditional-access-tou/16.png)

    a. Clique em **Aplicativos de nuvem**.

    b. Na **Página de aplicativos de nuvem**, clique em **Selecionar aplicativos**.

    c. Clique em **Selecionar**.

    d. Na página **Selecionar**, selecione **Microsoft Azure** e clique em **Selecionar**.

    e. Na página de aplicativos de nuvem, clique em **Concluído**.

3. Clique em **What If**.


## <a name="test-your-conditional-access-policy"></a>Testar sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para garantir que ela funcione conforme o esperado. 

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) usando sua conta de teste **Isabella Simonsen**. Você deve ver uma caixa de diálogo que exige que você aceite os termos de uso.

![Termos de uso](./media/active-directory-conditional-access-tou/57.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste e a política de acesso condicional:

- Caso não saiba como excluir um usuário do Azure AD, consulte [Excluir usuários do Azure AD](fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Para excluir sua política, selecione-a e clique em **Excluir** na barra de ferramentas de acesso rápido.

    ![Autenticação multifator](./media/active-directory-conditional-access-tou/33.png)

- Para excluir os termos de uso, selecione-o e, em seguida, clique em **excluir termos** na barra de ferramentas na parte superior. 

    ![Autenticação multifator](./media/active-directory-conditional-access-tou/29.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exige MFA para aplicativos específicos](./active-directory-conditional-access-app-based-mfa.md)
> [Bloquear acesso quando é detectado um risco da sessão](./active-directory-conditional-access-app-sign-in-risk.md)

