---
title: 'Tutorial: Integração do Azure Active Directory ao Soloinsight-CloudGate SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5839d7212958cffe45fae19f1e502ecf04e744a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162044"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Tutorial: Integração do Azure Active Directory ao Soloinsight-CloudGate SSO

Neste tutorial, você aprenderá a integrar o Soloinsight-CloudGate SSO ao Azure AD (Azure Active Directory).
A integração do Soloinsight-CloudGate SSO ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Soloinsight-CloudGate SSO.
* Você pode permitir que os usuários sejam conectados automaticamente ao Soloinsight-CloudGate SSO (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Soloinsight-CloudGate SSO, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Soloinsight-CloudGate SSO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Soloinsight-CloudGate SSO dá suporte ao SSO iniciado por **SP**

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando o Soloinsight-CloudGate SSO por meio da galeria

Para configurar a integração do Soloinsight-CloudGate SSO ao Azure AD, você precisará adicionar o Soloinsight-CloudGate SSO por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Soloinsight-CloudGate SSO por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Soloinsight-CloudGate SSO**, selecione **Soloinsight-CloudGate SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Soloinsight-CloudGate SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Soloinsight-CloudGate SSO, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Soloinsight-CloudGate SSO.

Para configurar e testar o logon único do Azure AD com o Soloinsight-CloudGate SSO, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Soloinsight-CloudGate SSO que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Soloinsight-CloudGate SSO, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Soloinsight-CloudGate SSO**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Soloinsight-CloudGate SSO](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, o que é explicado mais adiante na seção **Configurar o logon único do Soloinsight-CloudGate SSO** do tutorial.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Soloinsight-CloudGate SSO**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Configurar o logon único do Soloinsight-CloudGate SSO

1. Para obter os valores a serem colados no portal do Azure durante a Configuração Básica do SAML, faça logon no portal da Web do CloudGate usando suas credenciais e, em seguida, acesse as configurações de SSO, que podem ser encontradas no caminho **Página Inicial > Administração > Configurações do sistema > Geral**.

    ![Configurações de SSO do CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL do Consumidor do SAML**

    * Copie os links disponíveis nos campos **URL do Consumidor do SAML** e **URL de Redirecionamento** e cole-os na seção **Configuração Básica do SAML** no portal do Azure nos campos **Identificador (ID da Entidade)** e **URL de Resposta**, respectivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Certificado de Autenticação SAML**

    * Acesse a origem do arquivo de Certificado (Base64) baixado nas listas Certificado de Autenticação SAML do portal do Azure e clique com o botão direito do mouse nela. Escolha a opção **Editar com o Bloco de notas++** na lista. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no arquivo de Certificado (Base64) do Bloco de notas++.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo no campo **Certificado** das configurações de SSO do portal da Web do CloudGate e clique no botão Salvar.

        ![Portal de certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Grupo Padrão**

    * Selecione **Administrador de Negócios** na lista suspensa da opção **Grupo Padrão** no portal da Web do CloudGate

        ![Grupo padrão](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Identificador do AD e URL de Logon**

    * A **URL de Logon** copiada das configurações **Configurar o Soloinsight-CloudGate SSO** do portal do Azure deve ser inserida na seção de configurações de SSO do portal da Web do CloudGate. 

    * Cole o link da **URL de Logon** do portal do Azure no campo **URL de Logon do AD** do portal da Web do CloudGate.
     
    * Cole o link do **Identificador do Azure AD** do portal do Azure no campo **Identificador do AD** do portal da Web do CloudGate

        ![Logon do AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Soloinsight-CloudGate SSO.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Soloinsight-CloudGate SSO**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Soloinsight-CloudGate SSO**.

    ![O link do Soloinsight-CloudGate SSO na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada para o usuário na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar um usuário de teste do Soloinsight-CloudGate SSO

Para criar um usuário de teste, selecione **Funcionários** no menu principal do portal da Web do CloudGate e preencha o formulário Adicionar Novo Funcionário. O Nível de Autoridade que deve ser atribuído ao usuário de teste é **Administrador de Negócios**. Clique em **Criar** depois que todos os campos obrigatórios forem preenchidos.

![Teste de funcionário](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Soloinsight-CloudGate SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Soloinsight-CloudGate SSO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

