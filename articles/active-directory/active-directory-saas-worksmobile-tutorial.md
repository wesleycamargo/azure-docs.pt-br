---
title: "Tutorial: Integração do Azure Active Directory com o WORKS MOBILE | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o WORKS MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 215d1bf1f0f02e08f4d7bd65354d51bae945716e
ms.openlocfilehash: 4bd654821dfde2a59d9c9fd85166c7e9681791ab


---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Tutorial: Integração do Azure Active Directory com o WORKS MOBILE

Neste tutorial, você aprenderá a integrar o WORKS MOBILE ao Azure AD (Azure Active Directory).

A integração do WORKS MOBILE ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao WORKS MOBILE
- Você pode permitir que seus usuários façam logon automaticamente no WORKS MOBILE (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao WORKS MOBILE, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do WORKS MOBILE com logon único habilitado


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o WORKS MOBILE a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-works-mobile-from-the-gallery"></a>Adicionar o WORKS MOBILE a partir da galeria
Para configurar a integração do WORKS MOBILE ao Azure AD, você precisa adicionar o WORKS MOBILE a partir da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o WORKS MOBILE a partir da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **WORKS MOBILE**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_001.png)

5. No painel de resultados, selecione **WORKS MOBILE** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o WORKS MOBILE, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do WORKS MOBILE é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no WORKS MOBILE.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no WORKS MOBILE.

Para configurar e testar o logon único do Azure AD com o WORKS MOBILE, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criação de um usuário de teste do WORKS MOBILE](#creating-a-works-mobile-test-user)**: para ter um equivalente de Britta Simon no WORKS MOBILE que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo WORKS MOBILE.

**Para configurar o logon único do Azure AD com o WORKS MOBILE, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **WORKS MOBILE**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_01.png)

3. Na seção **URLs e Domínio do WORKS MOBILE**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<your-subdomain>.worksmobile.com/jp/myservice`

    b. Na caixa de texto **Identificador**, digite o valor como `worksmobile.com`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Entre em contato com a [equipe de suporte do WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) para obter valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_03.png)     

5. Na caixa de diálogo **Criar um Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_300.png)

6. Na seção **Certificado de Autenticação SAML**, selecione **Ativar o novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_04.png)

7. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_05.png) 

9. Na seção **Configuração do WORKS MOBILE**, clique em **Configurar WORKS MOBILE** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_07.png)

10. Para que o SSO seja configurado para seu aplicativo, entre em contato com a [equipe de suporte do WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) e forneça o seguinte: 

    • O **Arquivo de certificado** baixado

    • A **URL do Serviço de Logon Único SAML**

    • A **ID da entidade SAML**

    • A **URL de Saída**
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 

4. Na página de diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Britta Simon**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** de Britta Simon.

    c. Selecione **Mostrar Senha** e anote o valor de **Senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-works-mobile-test-user"></a>Criar um usuário de teste do WORKS MOBILE

Nesta seção, você criará uma usuária chamada Britta Simon no WORKS MOBILE. Trabalhe com a [equipe de suporte do WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) para adicionar usuários à plataforma WORKS MOBILE.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo-lhe acesso ao WORKS MOBILE.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao WORKS MOBILE, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório, vá para **Aplicativos empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **WORKS MOBILE**.

    ![Configurar o logon único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_50.png) 

3. No menu à esquerda, clique em **Usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de usuários.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grupos**.

7. Clique no botão **Atribuir** na caixa de diálogo **Adicionar Atribuição**.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco WORKS MOBILE no Painel de Acesso, você será automaticamente conectado ao seu aplicativo WORKS MOBILE.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO5-->


