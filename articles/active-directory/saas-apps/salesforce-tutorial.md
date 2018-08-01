---
title: 'Tutorial: Integração do Azure Active Directory com o Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: jeedes
ms.openlocfilehash: a453e2d16edecda9753c2940a745b260a3a2b893
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160256"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integração do Azure Active Directory com o Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure AD (Azure Active Directory).

A integração do Salesforce ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Salesforce.
- Você pode permitir que seus usuários façam logon automaticamente no Salesforce (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Salesforce com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Salesforce da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria
Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Salesforce na galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Salesforce**, selecione **Salesforce** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Salesforce na lista de resultados](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Salesforce, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Salesforce.

No Salesforce, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Salesforce, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Salesforce](#create-a-salesforce-test-user)** – para ter um equivalente de Brenda Fernandes no Salesforce que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Salesforce.

**Para configurar o logon único do Azure AD com o Salesforce, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Salesforce**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na seção **URLs e Domínio do Salesforce**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do Salesforce](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/salesforce-tutorial/tutorial_general_400.png)

6. Na seção **configuração do Salesforce**, clique em **Configurar Salesforce** para abrir a janela **Configurar Logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Salesforce](./media/salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Abra uma nova guia no navegador e faça logon em sua conta de administrador do Salesforce.

8. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-tutorial/configure1.png)

9. Role para baixo até **CONFIGURAÇÕES** no painel de navegação e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso.png)

10. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Se não for possível habilitar as configurações de Logon Único para a conta do Salesforce, talvez seja necessário entrar em contato com a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support). 

11. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

      ![Configurar o logon único](./media/salesforce-tutorial/sf-enable-saml.png)
12. Para definir as configurações de logon único do SAML, clique em **Novo**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Na página **Edição de Configuração de Logon Único do SAML** , realize as seguintes configurações:

    ![Configurar o logon único](./media/salesforce-tutorial/sf-saml-config.png)

    a. Para o campo **Nome** , digite um nome amigável para essa configuração. Fornecer um valor para **Nome** preenche automaticamente a caixa de texto **Nome da API**.

    b. No campo **Emissor**, cole o valor da **ID de Entidade do SAML** que você copiou do portal do Azure.

    c. Na **caixa de texto Id da Entidade**, digite seu nome de domínio do Salesforce usando o seguinte padrão:

      * Conta empresarial: `https://<subdomain>.my.salesforce.com`
      * Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    d. Para carregar o **Certificado de Provedor de Identidade**, clique em **Escolher Arquivo** para navegar e selecionar o arquivo do certificado que você baixou do portal do Azure.

    e. Como **Tipo de Identidade SAML**, escolha uma das seguintes opções:

      * Selecione **A declaração contém o nome do usuário do Salesforce**, se o nome do usuário do Salesforce estiver sendo transmitido na declaração SAML

      * Selecione **A declaração contém a ID de Federação do objeto de Usuário**, se a ID de Federação do objeto de Usuário estiver sendo transmitida na declaração SAML

      * Selecione **A declaração contém a ID de Usuário do objeto de Usuário**, se a ID de Usuário do objeto de Usuário estiver sendo transmitida na declaração SAML

    f. Para **Local de Identidade do SAML**, selecione **A identidade está no elemento NameIdentifier da declaração Subject**.

    g. Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **Redirecionamento HTTP**.

    h. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único** copiado do Portal do Azure

    i. Finalmente, clique em **Salvar** para aplicar as configurações de logon único do SAML.

14. No painel de navegação à esquerda no Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-my-domain.png)

15. Role para baixo até a seção **Configuração de Autenticação** e, em seguida, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-edit-auth-config.png)

16. Na seção **Configuração de Autenticação**, selecione **AzureSSO** como **Serviço de Autenticação** da configuração de SSO de SAML e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, quando os usuários tentarem iniciar o logon único em seu ambiente do Salesforce, serão solicitados a selecionar o serviço de autenticação com o qual desejam entrar. Se você não quiser que isso aconteça, deverá **deixar todos os outros serviços de autenticação desmarcados**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/salesforce-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/salesforce-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/salesforce-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/salesforce-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-salesforce-test-user"></a>Criar um usuário de teste do Salesforce

Nesta seção, é criado um usuário denominado Brenda Fernandes no Salesforce. O Salesforce dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce. O Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Salesforce.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Salesforce, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Salesforce**.

    ![O link do Salesforce na lista de Aplicativos](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Salesforce no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Salesforce.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png
