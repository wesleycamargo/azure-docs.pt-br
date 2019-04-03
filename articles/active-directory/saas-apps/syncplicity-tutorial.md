---
title: 'Tutorial: Integração do Azure Active Directory ao Syncplicity | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 11ac9d859ec76c782f762dfc6f424d70ea487665
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360221"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Azure Active Directory ao Syncplicity

Neste tutorial, você aprenderá a integrar o Syncplicity ao Azure AD (Azure Active Directory).
A integração do Syncplicity ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Syncplicity.
* É possível permitir que seus usuários entrem automaticamente no Syncplicity (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Syncplicity, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Syncplicity habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Syncplicity é compatível com o SSO iniciado por **SP**

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar o Syncplicity da galeria

Para configurar a integração do Syncplicity ao Azure AD, você precisará adicionar o Syncplicity da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Syncplicity da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Syncplicity**, selecione **Syncplicity** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Syncplicity na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Syncplicity, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Syncplicity.

Para configurar e testar o logon único do Azure AD com o Syncplicity, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do Syncplicity](#configure-syncplicity-single-sign-on)** – para configurar as definições de Logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Syncplicity](#create-syncplicity-test-user)** – para ter um equivalente de Brenda Fernandes no Syncplicity que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Syncplicity, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Syncplicity**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Syncplicity](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Syncplicity](https://www.syncplicity.com/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Syncplicity**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-syncplicity-single-sign-on"></a>Configurar logon único do Syncplicity

1. Entre no locatário do **Syncplicity** .

1. No menu, na parte superior, clique em **administrador**, selecione **configurações** e clique em **Domínio personalizado e logon único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na página do diálogo **SSO (Logon Único)** , realize as seguintes etapas:

    ![Logon Único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

     a. Na caixa de texto **Domínio Personalizado** , digite o nome do seu domínio.
  
    b. Selecione **Habilitado** como **Status do Logon Único**.

    c. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.

    d. Na caixa de texto **URL da página de entrada**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **URL da página de logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Em **Certificado do Provedor de Identidade**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do portal do Azure.

    g. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Syncplicity.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **Syncplicity**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Syncplicity**.

    ![O link do Syncplicity na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.
    ![O link "Usuários e grupos"](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-syncplicity-test-user"></a>Criar usuário de teste do Syncplicity

Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do AAD no Syncplicity.

**Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:**

1. Faça logon no locatário do **Syncplicity** (por exemplo, `https://company.Syncplicity.com`).

1. Clique em **admin** e selecione **contas de usuário** e, em seguida, clique em **ADICIONAR UM USUÁRIO**.

    ![Gerenciar Usuários](./media/syncplicity-tutorial/ic769764.png "Gerenciar Usuários")

1. Digite os **Endereços de email** de uma conta do Azure AD que você deseja provisionar, selecione **Usuário** como **Função** e clique em **AVANÇAR**.

    ![Dados da Conta](./media/syncplicity-tutorial/ic769765.png "Dados da Conta")

    > [!NOTE]
    > O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.

1. Selecione um grupo em sua empresa do qual o novo usuário deverá se tornar membro e clique em **AVANÇAR**.

    ![Associação a um Grupo](./media/syncplicity-tutorial/ic769772.png "Associação a um Grupo")

    > [!NOTE]
    > Se não houver nenhum grupo listado, basta clicar em **AVANÇAR**.

1. Selecione as pastas que você gostaria de colocar sob o controle do Syncplicity no computador do usuário e clique em **AVANÇAR**.

    ![Pastas do Syncplicity](./media/syncplicity-tutorial/ic769773.png "Pastas do Syncplicity")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Syncplicity ou as APIs fornecidas pelo Syncplicity para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Syncplicity no Painel de Acesso, você deverá entrar automaticamente no Syncplicity no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
