---
title: 'Tutorial: Integração do Azure Active Directory ao Knowledge Anywhere LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: c56c03637e5a8eb7716673974292482a9fb75212
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877785"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Tutorial: Integração do Azure Active Directory ao Knowledge Anywhere LMS

Neste tutorial, você aprenderá a integrar o Knowledge Anywhere LMS ao Azure AD (Azure Active Directory).
A integração do Knowledge Anywhere LMS ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Knowledge Anywhere LMS.
* Você pode permitir que os usuários sejam conectados automaticamente ao Knowledge Anywhere LMS (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Knowledge Anywhere LMS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Knowledge Anywhere LMS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Knowledge Anywhere LMS dá suporte ao SSO iniciado por **SP e IDP**
* O Knowledge Anywhere LMS dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionando o Knowledge Anywhere LMS por meio da galeria

Para configurar a integração do Knowledge Anywhere LMS ao Azure AD, você precisará adicionar o Knowledge Anywhere LMS por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Knowledge Anywhere LMS por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Knowledge Anywhere LMS**, selecione **Knowledge Anywhere LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Knowledge Anywhere LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Knowledge Anywhere LMS, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Knowledge Anywhere LMS.

Para configurar e testar o logon único do Azure AD com o Knowledge Anywhere LMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)** – para ter um equivalente de Brenda Fernandes no Knowledge Anywhere LMS que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Knowledge Anywhere LMS, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Knowledge Anywhere LMS**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Knowledge Anywhere LMS](common/idp-intiated.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, explicados mais adiante no tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Knowledge Anywhere LMS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Knowledge Anywhere LMS**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Configurar o logon único do Knowledge Anywhere LMS

1. Em outra janela do navegador, abra o portal de administração do Knowledge Anywhere LMS.

2. Selecione a guia **Site**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Selecione a guia **Configurações do SAML**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Clique em **Adicionar Novo**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Na página **Adicionar/Atualizar Configurações do SAML**, execute as seguintes etapas:

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

     a. Insira o nome do IdP de acordo com sua organização. Por exemplo: `Azure`.

    b. Na caixa de texto **ID da Entidade do IdP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL do IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o arquivo de certificado baixado do portal do Azure no Bloco de notas, copie o conteúdo do certificado e cole-o na caixa de texto **Certificado**.

    e. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Selecione **Site Principal** na lista suspensa do **Domínio**.

    g. Copie o valor da **ID da Entidade do SP** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica do SAML** no portal do Azure.

    h. Copie o valor da **URL de Resposta do SP (ACS)** e cole-o na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    i. Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Knowledge Anywhere LMS.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Knowledge Anywhere LMS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Knowledge Anywhere LMS**.

    ![O link do Knowledge Anywhere LMS na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar um usuário de teste do Knowledge Anywhere LMS

Nesta seção, um usuário chamado Brenda Fernandes será criado no Knowledge Anywhere LMS. O Knowledge Anywhere LMS dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Knowledge Anywhere LMS, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Knowledge Anywhere LMS no Painel de Acesso, você deverá ser conectado automaticamente ao Knowledge Anywhere LMS, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)