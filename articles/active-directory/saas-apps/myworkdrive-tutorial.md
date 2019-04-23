---
title: 'Tutorial: Integração do Azure Active Directory ao MyWorkDrive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: d16aa8442f71845e7b46377c6c290212f9c400a3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280495"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Tutorial: Integração do Azure Active Directory ao MyWorkDrive

Neste tutorial, você aprenderá como integrar o MyWorkDrive ao Microsoft Azure AD (Azure Active Directory).
A integração do MyWorkDrive ao Microsoft Azure AD oferece os seguintes benefícios:

* É possível controlar no Microsoft Azure AD quem tem acesso ao MyWorkDrive.
* Você pode permitir que seus usuários entrem automaticamente no MyWorkDrive (logon único) usando suas contas do Microsoft Azure Active Directory.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao MyWorkDrive, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura com logon único do MyWorkDrive habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O MyWorkDrive dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionar o MyWorkDrive da galeria

Para configurar a integração do MyWorkDrive ao Microsoft Azure AD, será necessário adicionar o MyWorkDrive por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MyWorkDrive por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **MyWorkDrive**, selecione **MyWorkDrive** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![MyWorkDrive na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o MyWorkDrive, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do MyWorkDrive.

Para configurar e testar o logon único do Microsoft Azure AD com o MyWorkDrive, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do MyWorkDrive](#configure-myworkdrive-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do MyWorkDrive](#create-myworkdrive-test-user)** – para ter um equivalente de Brenda Fernandes no MyWorkDrive que esteja vinculado à representação de usuário no Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o MyWorkDrive, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **MyWorkDrive**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    ![Informações de logon único de Domínio e URLs do MyWorkDrive](common/both-replyurl.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do MyWorkDrive](common/both-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Insira o nome do host do MyWorkDrive Server da sua empresa: por exemplo,
    > 
    > URL de Resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de logon:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Entre em contato com a [equipe de suporte do MyWorkDrive](mailto:support@myworkdrive.com) se você não tiver certeza de como configurar seu próprio nome de host e certificado SSL para esses valores.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

### <a name="configure-myworkdrive-single-sign-on"></a>Configurar o logon único do MyWorkDrive

1. Em uma janela de navegador da web diferente, faça logon como administrador de segurança do MyWorkDrive.

2. No MyWorkDrive Server no painel de administração, clique em **ENTERPRISE** e execute as seguintes etapas:

    ![O administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

     a. Habilitar **SSO de SAML/ADFS**.

    b. Selecione **SAML - Microsoft Azure Active Directory**

    c. Na caixa de texto **Url Metadados da Federação de Aplicativos do Azure**, cole o valor de **URL de Metadados da Federação de Aplicativos** que você copiou do portal do Azure.

    d. Clique em **Salvar**

    >[!NOTE]
    >Para obter informações adicionais, consulte o [artigo de suporte do MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao MyWorkDrive.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **MyWorkDrive**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **MyWorkDrive**.

    ![O link do MyWorkDrive na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-myworkdrive-test-user"></a>Criar usuário de teste do MyWorkDrive

Nesta seção, você criará uma usuária chamada Brenda Fernandes no MyWorkDrive. Trabalhe com a [equipe de suporte do MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os usuários na plataforma do MyWorkDrive. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MyWorkDrive no Painel de Acesso, você deverá ser conectado automaticamente ao MyWorkDrive no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

