---
title: 'Tutorial: Integração do Azure Active Directory com o Way We Do | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f176ce43e4d7162c664115a6fd3ce9369fe79a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270057"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Tutorial: Integração do Azure Active Directory com o Way We Do

Neste tutorial, você aprenderá a integrar o Way We Do ao Azure AD (Azure Active Directory).
A integração do Way We Do ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Way We Do.
* Você pode permitir que os usuários sejam conectados automaticamente ao Way We Do (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Way We Do, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Way We Do

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Way We Do dá suporte ao SSO iniciado por **SP**

* O Way We Do dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-way-we-do-from-the-gallery"></a>Como adicionar o Way We Do da galeria

Para configurar a integração do Way We Do ao Azure AD, você precisará adicionar o Way We Do da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Way We Do por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Way We Do**, selecione **Way We Do**  no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Way We Do na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Way We Do, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Way We Do.

Para configurar e testar o logon único do Azure AD com o Way We Do, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o logon único do Way We Do](#configure-way-we-do-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Way We Do](#create-way-we-do-test-user)** – para ter um equivalente de Brenda Fernandes no Way We Do que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Way We Do, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Way We Do**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URL e Domínio do Way We Do](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [Equipe de suporte ao cliente do Way We Do](mailto:support@waywedo.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o Way We Do**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-way-we-do-single-sign-on"></a>Configurar o logon único do Way We Do

1. Em outra janela do navegador da Web, entre no Way We Do como Administrador de segurança.

2. Clique no **ícone de pessoa** no canto superior direito de qualquer página do Way We Do e, em seguida, clique em **Conta** no menu suspenso.

    ![Conta do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Clique no **ícone do menu** para abrir o menu de navegação por push e clique em **Logon Único**.

    ![Único do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Na página **Configuração de logon único**, execute as seguintes etapas:

    ![Salvar no Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

     a. Clique na opção **Ativar logon único** para defini-la como **Sim** para habilitar o Logon Único.

    b. Na caixa de texto **Nome de logon único**, insira seu nome.

    c. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Na caixa de texto **URL do SSO SAML**, cole o valor de **URL de logon**, copiado do portal do Azure.

    e. Carregue o certificado clicando no botão **selecionar** ao lado de **Certificado**.

    f. **Configurações Opcionais** -
    
    * Habilitar senhas – quando essa opção estiver desabilitada, a senha regular funcionará para o Way We Do, de modo que os usuários só poderão usar o logon único.

    * Habilitar o provisionamento automático – quando essa opção está habilitada, o endereço de email usado no logon será comparado automaticamente à lista de usuários no Way We Do. Se o endereço de email não corresponder a um usuário ativo no Way We Do, ele adicionará automaticamente uma nova conta de usuário para a pessoa que está entrando, solicitando quaisquer informações ausentes.

      > [!NOTE]
      > Os usuários adicionados por meio de logon único são adicionados como usuários gerais e não são atribuídos a uma função no sistema. Um Administrador pode entrar e modificar sua função como um editor ou administrador e também pode atribuir uma ou várias funções do Organograma. 

    g. Clique em **Salvar** para manter suas configurações.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Way We Do.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Way We Do**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Way We Do**.

    ![O link do Way We Do na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-way-we-do-test-user"></a>Criar um usuário de teste do Way We Do

Nesta seção, um usuário chamado Brenda Fernandes será criado no Way We Do. O Way We Do dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Way We Do, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do Way We Do](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Way We Do no Painel de Acesso, você deverá ser conectado automaticamente ao Way We Do, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

