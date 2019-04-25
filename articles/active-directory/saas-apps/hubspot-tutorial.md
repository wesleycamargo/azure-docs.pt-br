---
title: 'Tutorial: Integração do Azure Active Directory ao HubSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adcd0f094d584e770f1a3f4938ee677ba58a21a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995686"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Azure Active Directory ao HubSpot

Neste tutorial, você aprenderá a integrar o HubSpot ao Azure AD (Azure Active Directory).
A integração do HubSpot ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao HubSpot.
* Você pode permitir que os usuários sejam conectados automaticamente ao HubSpot (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HubSpot, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do HubSpot habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O HubSpot dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-hubspot-from-the-gallery"></a>Adição do HubSpot por meio da galeria

Para configurar a integração do HubSpot ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HubSpot por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HubSpot**, selecione **HubSpot** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o HubSpot, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HubSpot.

Para configurar e testar o logon único do Azure AD com o HubSpot, você precisa concluir os seguintes passos básicos:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do HubSpot](#configure-hubspot-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do HubSpot](#create-hubspot-test-user)** – para ter um equivalente de Brenda Fernandes no HubSpot que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o HubSpot, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **HubSpot**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações sobre logon único de domínio e URLs do HubSpot](common/idp-intiated.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e o Identificador reais, que são explicados adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações sobre logon único de domínio e URLs do HubSpot](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.hubspot.com/login`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar HubSpot**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-hubspot-single-sign-on"></a>Configurar logon único do HubSpot

1. Abra uma nova guia no navegador e entre em sua conta de administrador do HubSpot.

2. Clique no **ícone de configurações** no canto superior direito da página.

    ![Configurar o logon único](./media/hubspot-tutorial/config1.png)

3. Clique em **Padrões da Conta**.

    ![Configurar o logon único](./media/hubspot-tutorial/config2.png)

4. Role para baixo até a seção **Segurança** e clique em **Configurar**.

    ![Configurar o logon único](./media/hubspot-tutorial/config3.png)

5. Na seção **Configurações de logon único**, execute as seguintes etapas:

    ![Configurar o logon único](./media/hubspot-tutorial/config4.png)

     a. Clique no botão **Cópia** para copiar o valor **URl de audiência (ID da entidade do provedor de serviço)** e cole-o na caixa de texto **Identificador** na seção **Configuração básica de SAML** no Portal do Azure.

    b. Clique no botão **}copiar** para copiar o valor **URl, ACS, Destinatário ou Redirecionamento de Logon** e cole-o na caixa de texto **URL de resposta** na seção **Configuração básica de SAML** no Portal do Azure.

    c. Na caixa de texto **Identificador do Provedor de Identidade ou URL do Emissor**, cole o valor do **Identificador do Azure AD**, que você copiou do Portal do Azure.

    d. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do Portal do Azure.

    e. Abra o arquivo **Certificate(Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa **Certificado X.509**.

    f. Clique em **Verificar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso à HubSpot.

1. No portal do Azure, escolha **Aplicativos Empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **HubSpot**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **HubSpot**.

    ![O link do HubSpot na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-hubspot-test-user"></a>Criar usuário de teste do HubSpot

Para permitir que os usuários do Azure AD entrem no HubSpot, eles devem ser provisionados no HubSpot. No caso do HubSpot, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa da **HubSpot** como administrador.

2. Clique no **ícone de configurações** no canto superior direito da página.

    ![Configurar o logon único](./media/hubspot-tutorial/config1.png)

3. Clique em **Usuários e Equipes**.

    ![Configurar o logon único](./media/hubspot-tutorial/user1.png)

4. Clique em **Criar usuário**.

    ![Configurar o logon único](./media/hubspot-tutorial/user2.png)

5. Insira o endereço de email do usuário como `brittasimon\@contoso.com` na caixa de texto **Adicionar endereços de email** e clique em **Avançar**.

    ![Configurar o logon único](./media/hubspot-tutorial/user3.png)

6. Na seção **Criar usuários**, analise cada guia individual, selecione as opções e as permissões apropriadas para o usuário e clique em **Avançar**.

    ![Configurar o logon único](./media/hubspot-tutorial/user4.png)

7. Clique em **Enviar** para enviar o convite para o usuário.

    ![Configurar o logon único](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O usuário será ativado depois de aceitar o convite.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do HubSpot no Painel de Acesso, você deverá ser conectado automaticamente ao HubSpot, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

