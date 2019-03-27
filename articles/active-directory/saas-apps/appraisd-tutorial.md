---
title: 'Tutorial: Integração do Azure Active Directory ao Appraisd | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7d1e3511bf74650ddc9dca1cf77c2b7c297c96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861495"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Tutorial: Integração do Azure Active Directory ao Appraisd

Neste tutorial, você aprende a integrar o Appraisd ao Azure Active Directory (Azure AD).
A integração do Appraisd ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Azure Active Directory quem terá acesso ao Appraisd.
* Você pode permitir que os usuários sejam conectados automaticamente ao Appraisd (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Appraisd, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Appraisd

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Appraisd dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando o Appraisd por meio da galeria

Para configurar a integração do Appraisd ao Azure Active Directory você precisará adicionar o Appraisd da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Appraisd da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Appraisd**, selecione **Appraisd** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Appraisd na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Appraisd, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Appraisd.

Para configurar e testar o logon único do Azure Active Directory com o Appraisd, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Appraisd](#configure-appraisd-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Appraisd](#create-appraisd-test-user)** – para ter um equivalente de Brenda Fernandes no Appraisd que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com Appraisd, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Appraisd**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Appraisd](common/both-preintegrated-advanced-urls.png)

     a. Clique em **Definir URLs adicionais**.

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `<TENANTCODE>`

    c. Se você deseja configurar o aplicativo no modo iniciado pelo **SP**, na caixa de texto **URL de Logon** digite uma URL usando o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Você pode obter o valor real de URL de logon e o estado de retransmissão na página de configuração de SSO Appraisd que é explicada posteriormente no tutorial.

5. O aplicativo Appraisd espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME |  Atributo de Origem|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Appraisd**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-appraisd-single-sign-on"></a>Configurar o logon único do Appraisd

1. Em uma janela de navegador da web diferente, entre no Appraisd como administrador de segurança.

2. No canto superior direito da página, clique no ícone **Configurações** e, em seguida, navegue para **Configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. Do lado esquerdo do menu, clique em **Logon único SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Na página **Configuração de Logon Único SAML 2.0**, realize as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

     a. Copie o valor **Estado de Retransmissão Padrão** e cole-o na caixa de texto **Estado de Retransmissão** em **Configuração Básica do SAML** no portal do Azure.

    b. Copie o valor **URL de logon iniciado pelo serviço** e cole-o na caixa de texto **URL de logon** em **Configuração Básica do SAML** no portal do Azure.

5. Role para baixo em sintonia **identificando usuários**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

     a. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do portal do Azure e clique em **Salvar**.

    b. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do portal do Azure e clique em **Salvar**.

    c. No Bloco de notas, abra o certificado codificado em base 64 baixado do portal do Azure, copie o conteúdo e, em seguida, cole-o na caixa **Certificado X.509** e clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Appraisd.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Appraisd**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Appraisd**.

    ![O link do Appraisd na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-appraisd-test-user"></a>Criar um usuário de teste do Appraisd

Para habilitar o Azure Active Directory, os usuários entram no Appraisd, eles devem ser provisionados no Appraisd. No Appraisd, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Appraisd como um Administrador de Segurança.

2. No canto superior direito da página, clique no ícone **Configurações** e, em seguida, navegue para **Centro de administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas na parte superior da página, clique em **Pessoas** e, em seguida, navegue para **Adicionar um novo usuário**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na página **Adicionar um novo usuário**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

     a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **simon**.

    c. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    d. Clique em **Adicionar usuário**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Appraisd no Painel de Acesso, você deverá ser conectado automaticamente ao Appraisd, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
