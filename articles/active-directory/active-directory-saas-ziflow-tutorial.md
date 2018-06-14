---
title: 'Tutorial: Integração do Azure Active Directory ao Ziflow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 4f667edbf6b0fab8c8dac4442d50d14252c4c4cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354234"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integração do Microsoft Azure Active Directory ao Ziflow

Neste tutorial, você aprende a integrar o Ziflow ao Azure AD (Azure Active Directory).

A integração do Ziflow ao Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem terá acesso ao Ziflow.
- Você pode permitir que usuários façam logon automaticamente no Ziflow (logon único) com as respectivas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com o Ziflow, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único no Ziflow

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Ziflow da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-ziflow-from-the-gallery"></a>Adição do Ziflow da galeria
Para configurar a integração do Ziflow ao Microsoft Azure AD, você precisará adicionar o Ziflow da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Ziflow da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Ziflow**, selecione **Ziflow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Ziflow na lista de resultados](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Ziflow, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do Ziflow é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no Ziflow.

Para configurar e testar o logon único do Microsoft Azure AD com o Ziflow, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Ziflow](#create-a-ziflow-test-user)** – para ter um equivalente de Brenda Fernandes no Ziflow que esteja vinculado à representação de usuário do Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no Portal do Azure e configurará o logon único no aplicativo portal Ziflow.

**Para configurar o logon único do Microsoft Azure AD com o Ziflow, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Ziflow**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Na seção **URLs e Domínio do Ziflow**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Os valores anteriores não são reais. Você atualizará o valor de ID exclusiva no identificador e na URL de logon com o valor real, que é explicado no tutorial posteriormente. Entre em contato com a [equipe de suporte do Ziflow](mailto:support@ziflow.com) para o valor de subdomínio na URL de logon.
    
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-ziflow-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Ziflow**, clique em **Configurar Ziflow** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no Ziflow como Administrador de segurança.


8. Clique em Avatar no canto superior direito e, em seguida, clique em **Gerenciar conta**.

    ![Gerenciar configuração do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_manage.png)

9. No canto superior esquerdo, clique em **Logon Único**.

    ![Logon único nas configuração do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_signon.png)

10. Na página **Logon Único**, execute as seguintes etapas:

    ![Logon único nas configuração do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecione **Tipo** como **SAML 2.0**.

    b. Na caixa de texto **URL de entrada**, cole o valor de **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.

    c. Carregue o certificado codificado em base 64 que você baixou do Portal do Azure, para o **Certificado de Autenticação do X509**.

    d. Na caixa de texto **URL de saída**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    e. Da seção **Definições de configuração para seu provedor de identificador**, copie o valor de ID exclusiva destacado e acrescente-o ao identificador e à URL de logon na **seção Domínio e URLs do Ziflow** no Portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-ziflow-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-ziflow-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-ziflow-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-ziflow-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-ziflow-test-user"></a>Criar um usuário de teste no Ziflow

Para permitir que os usuários do Microsoft Azure AD façam logon no Ziflow, eles devem ser provisionados no Ziflow. No Ziflow, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon no Ziflow como Administrador de Segurança.

2. Navegue até **Pessoas** na parte superior.

    ![Configuração pessoas do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_people.png)

3. Clique em **Adicionar** e depois em **Adicionar usuário**.

    ![Configuração adicionar usuários do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_add.png)

4. Na janela pop-up **Adicionar um Usuário**, realize as seguintes etapas:

    ![Configuração adicionar usuários do Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Na caixa de texto **Email**, insira o email do usuário como brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como Brenda.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como Fernandes.

    d. Selecione sua função no Ziflow.

    e. Clique em **Adicionar 1 usuário**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Ziflow.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Ziflow, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Ziflow**.

    ![O link do Ziflow na lista de Aplicativos](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Ziflow no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Ziflow.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_203.png

