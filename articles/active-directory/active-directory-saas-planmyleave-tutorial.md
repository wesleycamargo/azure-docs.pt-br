---
title: "Tutorial: integração do Azure Active Directory ao PlanMyLeave | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o PlanMyLeave."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Tutorial: integração do Azure Active Directory com o PlanMyLeave

Neste tutorial, você aprenderá a integrar o PlanMyLeave ao Azure AD (Azure Active Directory).

A integração do PlanMyLeave ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você poderá controlar quem tem acesso ao PlanMyLeave
- Você pode permitir que seus usuários façam logon automaticamente no PlanMyLeave (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o PlanMyLeave, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do PlanMyLeave com logon único habilitado


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando PlanMyLeave da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-planmyleave-from-the-gallery"></a>Adicionando PlanMyLeave da galeria
Para configurar a integração do PlanMyLeave ao Azure AD, você precisará adicionar o PlanMyLeave da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PlanMyLeave por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **PlanMyLeave**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. No painel de resultados, selecione **PlanMyLeave** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o PlanMyLeave, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PlanMyLeave é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no PlanMyLeave.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no PlanMyLeave.

Para configurar e testar o logon único do Azure AD com o PlanMyLeave, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste PlanMyLeave](#creating-a-planmyleave-test-user)** – para ter um equivalente de Brenda Fernandes no PlanMyLeave que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo PlanMyLeave.

**Para configurar o logon único do Azure AD com o PlanMyLeave, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **PlanMyLeave**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na página da caixa de diálogo **Logon único**, como **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. Na seção **URLs e Domínio do PlanMyLeave**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company-name>.planmyleave.com/Login.aspx`
    
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Para obter esses valores, entre em contato com a [equipe de suporte do PlanMyLeave](mailto:support@planmyleave.com).

4. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. Na seção **Configuração do PlanMyLeave**, clique em **Configurar PlanMyLeave** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. Em outra janela do navegador da Web, faça logon no locatário do PlanMyLeave como administrador.

11. Acesse **Configuração do Sistema**. Em seguida, na seção **Gerenciamento de Segurança**, clique em **Configurações do SAML da Empresa**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. Na seção **Configurações de SAML**, clique no ícone do editor.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. Na seção **Atualizar Configuração do SAML**, execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  Na caixa de texto **URL de Logon**, insira o valor da **URL de Serviço de Logon Único SAML** do assistente de configuração de aplicativo do Azure AD.

    b.  Abra seu arquivo de certificado baixado no bloco de notas, copie somente o conteúdo entre ---Begin Certificate--- e ---End certificate---- na área de transferência e cole-o na caixa de texto **Certificado**.

    c. Defina "**Is Enable**" como "**Yes**".

    d. Clique em **Salvar**.



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-planmyleave-test-user"></a>Criação de um usuário de teste PlanMyLeave

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no PlanMyLeave. O PlanMyLeave dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o PlanMyLeave, caso ainda não exista.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do PlanMyLeave](mailto:support@planmyleave.com).



### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao PlanMyLeave.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao PlanMyLeave, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **PlanMyLeave**.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco PlanMyLeave no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo PlanMyLeave.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
