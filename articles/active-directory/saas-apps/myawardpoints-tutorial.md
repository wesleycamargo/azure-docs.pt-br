---
title: 'Tutorial: Integração do Azure Active Directory com My Award Points Top Sub/Top Team | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o My Award Points Top Sub/Top Team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeedes
ms.openlocfilehash: 479fcc0408021ff63dbcabe3734f60a4ad6d542f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247747"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Tutorial: Integração do Azure Active Directory com My Award Points Top Sub/Top Team

Neste tutorial, você aprenderá a integrar o My Award Points Top Sub/Top Team ao Azure AD (Azure Active Directory).

A integração de My Award Points Top Sub/Top Team com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso a My Award Points Top Sub/Top Team.
- Você pode habilitar seus usuários a fazerem logon automaticamente para My Award Points Top Sub/Top Team (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD a My Award Points Top Sub/Top Team, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único de My Award Points Top Sub/Top Team

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar My Award Points Top Sub/Top Team da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Como adicionar My Award Points Top Sub/Top Team da galeria

Para configurar a integração de My Award Points Top Sub/Top Team ao Azure AD, você precisará adicionar My Award Points Top Sub/Top Team da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar My Award Points Top Sub/Top Team da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **My Award Points Top Sub/Top Team**, selecione **My Award Points Top Sub/Top Team** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![My Award Points Top Sub/Top Team na lista de resultados](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com My Award Points Top Sub/Top Team com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário de My Award Points Top Sub/Top Team equivale a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em My Award Points Top Sub/Top Team precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com My Award Points Top Sub/Top Team, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste de Meus prêmios pontos principais](#create-a-my-award-points-top-subtop-team-test-user)** – para ter um equivalente de Brenda Fernandes em Meus prêmio pontos principais que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo My Award Points Top Sub/Top Team.

**Para configurar o logon único do Azure AD com My Award Points Top Sub/Top Team, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **My Award Points Top Sub/Top Team**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. Na seção **URLs e domínio de My Award Points Top Sub/Top Team**, realize as seguintes etapas:

    ![Informações de logon único de URLs e My Award Points Top Sub/Top Team](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Você obterá o valor `<SAMLENTITYID>` nas etapas posteriores deste tutorial.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. Na seção **My Award Points Top Sub/Top Team Configuration**, selecione **Configure My Award Points Top Sub/Top Team** para abrir a janela Configurar logon. Copie a ID da Entidade SAML da seção **Referência rápida** e anexe o valor da ID da Entidade SAML com a URL de logon no lugar de `<SAMLENTITYID>` na seção **URLs e domínio de My Award Points Top Sub/Top Team** no portal do Azure.

7. Para configurar o logon único em **My Award Points Top Sub/Top Team**, é necessário enviar o **XML de Metadados** para a [equipe de suporte de My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Criar um usuário de teste de My Award Points Top Sub/Top Team

Nesta seção, você criará um usuário chamado Brenda Fernandes em My Award Points Top Sub/Top Team. Trabalhe com a [equipe de suporte de My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com) para adicionar os usuários à plataforma My Award Points Top Sub/Top Team. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Brenda Fernandes a usar logon único do Azure concedendo acesso ao My Award Points Top Sub/Top Team.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes a My Award Points Top Sub/Top Team, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **My Award Points Top Sub/Top Team**.

    ![O link My Award Points Top Sub/Top Team na lista Aplicativos](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica na peça My Award Points Top Sub/Top Team no painel de acesso, deve ser conectado automaticamente ao seu aplicativo My Award Points Top Sub/Top Team.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png