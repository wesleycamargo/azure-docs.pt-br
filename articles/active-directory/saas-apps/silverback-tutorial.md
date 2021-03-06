---
title: 'Tutorial: Integração do Azure Active Directory com o Silverback | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1a104da9ecb28d2109e82056995ef7a8048eafe2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838741"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Azure Active Directory com o Silverback

Neste tutorial, você aprenderá a integrar o Silverback ao Microsoft Azure Active Directory.
A integração Silverback com o Microsoft Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Silverback.
* Você pode habilitar seus usuários ser automaticamente conectado no Silverback (logon único) com suas contas do AD do Azure.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Silverback, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único SilverBack

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Dá suporte a SilverBack **SP** SSO iniciado

## <a name="adding-silverback-from-the-gallery"></a>Adicionar o Silverback da galeria

Para configurar a integração do Silverback ao Microsoft Azure Active Directory você precisará adicionar o Silverback da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Silverback por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Silverback**, selecione **Silverback** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Silverback na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Silverback com base em um usuário de teste chamado **Britta Simon**.
Para logon único funcione, uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no Silverback precisa ser estabelecida.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Silverback, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único Silverback](#configure-silverback-single-sign-on)**  – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste Silverback](#create-silverback-test-user)**  – para ter um equivalente de Britta Simon no Silverback que esteja vinculado à representação de usuário do AD do Azure.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com Silverback, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), no **Silverback** página de integração de aplicativos, selecione **sign-on único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Silverback](common/sp-identifier-reply.png)

     a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `<YOURSILVERBACKURL>.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do Cliente Silverback](mailto:helpdesk@matrix42.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Configurar o logon único Silverback

1. Em um navegador da web diferente, faça logon em seu servidor Silverback como administrador.

2. Navegue até **Admin** > **provedor de autenticação**.

3. Na página **Configurações do Provedor de Autenticação**, execute as etapas a seguir:

    ![O administrador](./media/silverback-tutorial/tutorial_silverback_admin.png)

     a.  Clique em **Importar da URL**.

    b.  Cole a URL de metadados copiados e clique em **OK**.

    c.  Confirme com **OK** e em seguida, os valores serão preenchidos automaticamente.

    d.  Habilitar **Mostrar na página de logon**.

    e.  Habilitar **Criação Dinâmica de Usuárioa** se você quiser adicionar automaticamente por usuários do Microsoft Azure Active Directory autorizado (opcional).

    f.  Criar um **título** para o botão sobre o Portal de autoatendimento.

    g.  Carregar uma **ícone** clicando em **Escolher arquivo**.

    h.  Selecione o plano de fundo **cor** do botão.

    i.  Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Silverback.

1. No portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Silverback**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Silverback**.

    ![O link do Silverback na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-silverback-test-user"></a>Criar usuário de teste Silverback

Para habilitar os usuários do Microsoft Azure Active Directory no Silverback, devem ser provisionados no Silverback. No Silverback, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no servidor de processo como administrador.

2. Navegue até  **Usuários** e **adicione um novo usuário do dispositivo**.

3. Na página **Básico**, execute as seguintes etapas:

    ![O usuário](./media/silverback-tutorial/tutorial_silverback_user.png)

     a. Na caixa de texto **Nome de usuário**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    d. Na caixa de texto **Endereço de Email**, insira o email do usuário como **Brittasimon@contoso.com**.

    e. Na caixa de texto **Senha**, insira sua senha.

    f. Na caixa de texto **Confirmar senha**, digite sua senha novamente e confirme.

    g. Clique em **Salvar**.

> [!NOTE]
> Se você não quiser criar manualmente para cada usuário habilitar a **Criação Dinâmica de Usuário** caixa de seleção em **Admin** > **Provedor de Autenticação**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Silverback no painel de acesso, você deve ser conectado automaticamente ao Silverback para os quais você configura o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

