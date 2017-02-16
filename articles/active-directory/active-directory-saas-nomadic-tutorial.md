---
title: "Tutorial: integração do Azure Active Directory ao Nomadic | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Nomadic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13d02b1c-d98a-40b1-824f-afa45a2deb6a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ee8b12207ecb21bccc2670122565ba154aaa9ff8
ms.openlocfilehash: ce07a93977930852b49d4eaad4ad18254425a773


---
# <a name="tutorial-azure-active-directory-integration-with-nomadic"></a>Tutorial: Integração do Azure Active Directory com o Nomadic

Neste tutorial, você aprenderá a integrar o Nomadic ao Azure AD (Azure Active Directory).

A integração do Nomadic ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Nomadic
- Você pode permitir que os usuários façam logon automaticamente no Nomadic (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Nomadic, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Nomadic


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Nomadic da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-nomadic-from-the-gallery"></a>Adicionar o Nomadic da galeria
Para configurar a integração do Nomadic ao Azure AD, você precisa adicionar o Nomadic da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Nomadic por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **Aplicativos empresariais**. Em seguida, vá para **Todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Adicionar** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Nomadic**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_001.png)

5. No painel de resultados, selecione **Nomadic** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Nomadic, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Nomadic é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Nomadic.

Essa relação de vinculação é estabelecida por meio da atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Nomadic.

Para configurar e testar o logon único do Azure AD com o Nomadic, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Nomadic](#creating-a-nomadic-test-user)** - para ter um equivalente de Britta Simon no Nomadic vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Nomadic.

**Para configurar o logon único do Azure AD com o Nomadic, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Nomadic**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na página da caixa de diálogo **Logon único**, como **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_01.png)

3. Na seção **URLs e Domínio do Nomadic**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.nomadic.fm/signin`
    
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<company name>.nomadic.fm/auth/saml2/sp`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do Nomadic](mailto:help@nomadic.fm) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_03.png)     

5. Na caixa de diálogo **Criar Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-nomadic-tutorial/tutorial_general_300.png)

6. Na seção **Certificado de Autenticação SAML**, selecione **Ativar novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_04.png)

7. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar Logon Único](./media/active-directory-saas-nomadic-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_05.png) 

9. Para que o SSO seja configurado para o aplicativo, entre em contato com a [equipe de suporte do Nomadic](mailto:help@nomadic.fm) e forneça os **metadados** baixados.
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/create_aaduser_01.png) 

2. Acesse **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/create_aaduser_03.png) 

4. Na página da caixa de diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadic-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Britta Simon**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** de Britta Simon.

    c. Selecione **Mostrar Senha** e anote o valor da **Senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-nomadic-test-user"></a>Criar um usuário de teste do Nomadic

Nesta seção, você criará um usuário chamado Britta Simon no Nomadic. Trabalhe com a [equipe de suporte do Nomadic](mailto:help@nomadic.fm) para adicionar os usuários na plataforma do Nomadic.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Britta Simon use o logon único do Azure concedendo a ela acesso ao Nomadic.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao Nomadic, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório, vá para **Aplicativos empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Nomadic**.

    ![Configurar Logon Único](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_50.png) 

3. No menu à esquerda, clique em **Usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de usuários.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grupos**.

7. Clique no botão **Atribuir** na caixa de diálogo **Adicionar Atribuição**.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Nomadic no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo Nomadic.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


