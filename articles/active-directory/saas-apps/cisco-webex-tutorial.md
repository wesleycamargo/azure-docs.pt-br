---
title: 'Tutorial: Integração do Azure Active Directory ao Cisco Webex | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7629d602362b9a76691dbf79a0a8ecd874d750a9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189660"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integração do Azure Active Directory ao Cisco Webex

Neste tutorial, você aprenderá como integrar o Cisco Webex ao Azure AD (Azure Active Directory).
A integração do Cisco Webex ao Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Azure Active Directory quem tem acesso ao Cisco Webex.
* Você pode permitir que os usuários sejam conectados automaticamente ao Cisco Webex (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory ao Cisco Webex, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Cisco Webex

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cisco Webex dá suporte ao SSO iniciado por **SP**

* O Cisco Webex dá suporte ao provisionamento de usuário **Just-In-Time**

* O Cisco Webex dá suporte ao provisionamento de usuário **Automatizado**

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco Webex da galeria

Para configurar a integração do Cisco Webex ao Azure Active Directory, você precisa adicionar o Cisco Webex da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Webex por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco Webex**, selecione **Cisco Webex** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Cisco Webex na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Cisco Webex, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Webex.

Para configurar e testar o logon único do Azure AD com o Cisco Webex, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Cisco Webex](#configure-cisco-webex-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Cisco Webex](#create-cisco-webex-test-user)** – para ter um equivalente de Brenda Fernandes no Cisco Webex que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Cisco Webex, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco Webex**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Em uma janela diferente do navegador da Web, entre em seu site de empresa do Cisco Webex como administrador.

4. Clique em **Configurações** do lado esquerdo do menu.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Na página de configurações, na seção **Autenticação**, clique em **Modificar**.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Escolha **Integrar um provedor de identidade de terceiros. (Avançado)** e vá para a próxima tela.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Na página **Exportar Metadados de Diretório**, clique em **Baixar Arquivo de Metadados** para fazer o download o arquivo de metadados.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. No portal do Azure, na página **Configurar Logon Único com o SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

9. Na seção **Configuração Básica do SAML**, carregue o arquivo **Metadados do Provedor de Serviços** baixado e configure o aplicativo executando as seguintes etapas:

     a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Após a conclusão bem-sucedida do upload do arquivo de metadados do Provedor de Serviços, os valores de **Identificador** e **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML**:

    ![Informações de logon único em Domínio e URLs do Cisco Webex](common/sp-identifier-reply.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.webex.com/`

    > [!Note]
    > O valor da URL de Entrada não é real. Atualize esse valor com a URL de logon real. Contate a [equipe de suporte ao Cliente do Cisco Webex](https://www.webex.co.in/support/support-overview.html) para obter esse valor.

10. O aplicativo Cisco Webex espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

11. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | NOME | Atributo de Origem|
    | ---------------| --------------- | --------- |
    |   nome    | user.givenname |
    |   sobrenome    | user.surname |
    |   uid    | user.mail |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

12. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

13. Na seção **Configurar o Cisco Webex**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-cisco-webex-single-sign-on"></a>Configurar o Logon Único do Cisco Webex

1. Na página de administrador do site de empresa Cisco Webex, use a opção de navegador de arquivo para localizar e carregar o arquivo de metadados Microsoft Azure Active Directory. Em seguida, escolha **Exigir certificado assinado por uma autoridade de certificação em Metadados (mais seguro)** e vá para a próxima tela. 

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. Escolha **Testar Conexão SSO** e, quando uma nova guia do navegador for aberta, autentique-se com o Azure AD conectando-se.

3. Volte para a guia do navegador **Gerenciamento de Colaboração de Nuvem da Cisco**. Se o teste tiver sido bem-sucedido, escolha a opção **Este teste foi executado com êxito. Habilitar Logon Único** e clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Cisco Webex.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Cisco Webex**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Cisco Webex**.

    ![O link do Cisco Webex na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cisco-webex-test-user"></a>Criar um usuário de teste do Cisco Webex

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Cisco Webex. O Cisco Webex dá suporte ao provisionamento Just-In-Time e provisionamento automático de usuário, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Cisco Webex, um novo será criado quando você tentar acessar o Cisco Webex.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cisco Webex no Painel de Acesso, você deverá ser conectado automaticamente ao Cisco Webex, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

