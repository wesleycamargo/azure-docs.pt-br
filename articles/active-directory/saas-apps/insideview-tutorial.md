---
title: 'Tutorial: Integração do Azure Active Directory com o InsideView | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 76db7381985853a81843df812dffcd8ab2bd8f9a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407458"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Azure Active Directory com o InsideView

Neste tutorial, você aprenderá como integrar o InsideView ao Azure AD (Azure Active Directory).
A integração do InsideView ao Azure AD proporciona os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao InsideView.
* Você pode permitir que os usuários sejam conectados automaticamente ao InsideView (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao InsideView, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do InsideView

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O InsideView dá suporte ao SSO iniciado por **IDP**

## <a name="adding-insideview-from-the-gallery"></a>Como adicionar o InsideView por meio da galeria

Para configurar a integração do InsideView ao Azure AD, você precisará adicionar o InsideView por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o InsideView por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **InsideView**, selecione **InsideView** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![InsideView na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o InsideView, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do InsideView.

Para configurar e testar o logon único do Azure AD com o InsideView, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do InsideView](#configure-insideview-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do InsideView](#create-insideview-test-user)** – para ter um equivalente de Brenda Fernandes no InsideView que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o InsideView, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **InsideView**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do InsideView](common/idp-reply.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao cliente do InsideView](mailto:support@insideview.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o InsideView**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-insideview-single-sign-on"></a>Configurar o logon único do InsideView

1. Em outra janela do navegador da Web, entre em seu site de empresa do InsideView como administrador.

1. Na barra de ferramentas na parte superior, clique em **Administrador**, **Configurações de Logon Único** e em **Adicionar SAML**.
   
   ![Configurações de Logon Único do SAML](./media/insideview-tutorial/ic794135.png "Configurações de Logon Único do SAML")

1. Na seção **Adicionar um Novo SAML** , realize as seguintes etapas:

    ![Adicionar um Novo SAML](./media/insideview-tutorial/ic794136.png "Adicionar um Novo SAML")

     a. Na caixa de texto **Nome STS** , digite um nome para a sua configuração.

    b. Na caixa de texto  **Ponto de Extremidade não Solicitado SamlP/WS-Fed**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra seu certificado codificado em Base 64, que você baixou do Portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado STS**.

    d. Na caixa de texto **Mapeamento de ID de usuário do CRM**, digite `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na caixa de texto **Mapeamento de Email do CRM**, digite `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na caixa de texto **Mapeamento de Nome do CRM**, digite `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    g. Na caixa de texto **Mapeamento de Sobrenome do CRM**, digite `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao InsideView.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **InsideView**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **InsideView**.

    ![O link do InsideView na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-insideview-test-user"></a>Criar um usuário de teste do InsideView

Para permitir que os usuários do Azure AD entrem no InsideView, eles precisam ser provisionados no InsideView. No caso do InsideView, o provisionamento é uma tarefa manual.

Para obter os usuários ou contatos criados no InsideView, entre em contato com a [equipe de suporte do InsideView](mailto:support@insideview.com).

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do InsideView ou APIs fornecidas pelo InsideView para provisionar as contas de usuário do AD do Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do InsideView no Painel de Acesso, você será conectado automaticamente ao aplicativo InsideView, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
