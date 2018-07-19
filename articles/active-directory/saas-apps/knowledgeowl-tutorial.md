---
title: 'Tutorial: integração do Azure Active Directory ao KnowledgeOwl | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e4d86fc2e29c4d93567bedd045d57cd8d4e22cf5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052427"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Tutorial: integração do Active Directory do Azure ao KnowledgeOwl

Neste tutorial, você aprende a integrar o KnowledgeOwl ao Azure AD (Azure Active Directory).

A integração do KnowledgeOwl ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você poderá controlar quem tem acesso ao KnowledgeOwl.
- Você pode permitir que seus usuários façam logon automaticamente no KnowledgeOwl (Logon Único) com as contas deles do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao KnowledgeOwl, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do KnowledgeOwl

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o KnowledgeOwl da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionando o KnowledgeOwl da Galeria
Para configurar a integração do KnowledgeOwl ao Azure AD, você precisará adicionar o KnowledgeOwl da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o KnowledgeOwl da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **KnowledgeOwl**, selecione **KnowledgeOwl** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![KnowledgeOwl na lista de resultados](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o KnowledgeOwl, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do KnowledgeOwl é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no KnowledgeOwl.

Para configurar e testar o logon único do Azure AD com o KnowledgeOwl, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do KnowledgeOwl](#create-a-knowledgeowl-test-user)**: para ter um equivalente de Brenda Fernandes no KnowledgeOwl que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo do KnowledgeOwl.

**Para configurar o logon único do Azure AD com o KnowledgeOwl, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **KnowledgeOwl**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Na seção **Domínio e URLs do KnowledgeOwl**, execute as etapas a seguir se você deseja configurar o aplicativo no modo iniciado **IDP**:

    ![Informações de logon único no Domínio e nas URLs do KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único no Domínio e nas URLs do KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esse valor do Identificador, das URLs de resposta e da URL de entrada reais que são explicados posteriormente no tutorial.

5. O aplicativo KnowledgeOwl espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo.

    ![Configurar o logon único](./media/knowledgeowl-tutorial/attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
    
    | Nome do atributo | Valor do atributo | Namespace|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
    
    ![Configurar o logon único](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Na lista **Namespace**, digite o valor de namespace mostrado para essa linha.
    
    e. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do KnowledgeOwl**, clique em **Configurar KnowledgeOwl** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do KnowledgeOwl como administrador.

11. Clique em **Configurações** e, em seguida, selecione **Segurança**.

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

12. Role para baixo até a **Integração de SSO do SAML** e execute as seguintes etapas:
    
    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Selecione **Habilitar SSO do SAML**.

    b. Copie o valor da **ID da Entidade SP** e cole-o no **Identificador (ID da Entidade)**, que está na seção **Domínio e URLs do KnowledgeOwl** no portal do Azure.

    c. Copie o valor da **URL do Logon SP** e cole-o na **URL de entrada e URL de resposta**, que está nas caixas de texto na seção **Domínio e URLs do KnowledgeOwl** no portal do Azure.

    d. Na caixa de texto **IdP entityID**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    e. Na caixa de texto **URL de Logon do IdP**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    f. Na caixa de texto **URL de Logoff do IdP**, cole o valor da **URL de Saída** copiado do portal do Azure

    g. Carregue o formulário de certificado baixado do portal do Azure clicando em **Carregar certificado de IdP**.

    h. Clique em **Mapa de atributos SAML** para mapear atributos e executar as seguintes etapas:
    
    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` na caixa de texto **ID do SSO**
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` na caixa de texto **Email/Nome de usuário**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` na caixa de texto **Nome**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` na caixa de texto **Sobrenome**.
    * Clique em **Salvar**

    i. Na parte inferior da página, clique em **Salvar** .

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/knowledgeowl-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/knowledgeowl-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Criar um usuário de teste KnowledgeOwl

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no KnowledgeOwl. O KnowledgeOwl dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o KnowledgeOwl, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao KnowledgeOwl.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao KnowledgeOwl, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **KnowledgeOwl**.

    ![O link do KnowledgeOwl na lista de Aplicativos](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco KnowledgeOwl no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo do KnowledgeOwl.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

