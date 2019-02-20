---
title: 'Tutorial: Integração do Azure Active Directory com o Workspot Control | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Workrite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8716d018756d1e6eadcd6ebeeaf4f67ad0bc4741
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211148"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração do Azure Active Directory com o Workspot Control

Neste tutorial, você aprenderá a integrar o Controle do Workspot ao Azure AD (Azure Active Directory).

A integração do controle do Workspace ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Controle do Workspot.
- Você pode permitir que seus usuários façam logon automaticamente no Controle do Workspot (Logon Único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o controle Workspot, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Assinatura habilitada para um controle Workspot o logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o controle do workspot da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-workspot-control-from-the-gallery"></a>Adicionando o controle do workspot da galeria
Para configurar a integração do Controle do Workspot ao Azure AD, você precisa adicionar o Controle do Workspot da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o controle Workspot da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Controle do Workspot**, selecione **Controle do Workspot** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Controle do Workspot com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contrapartida no Controle do Workspot é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado no Controle do Workspot precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Controle do Workspot, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[ Crie um usuário de teste do Controle de Works](#create-a-workspot-control-test-user)** - para ter um equivalente de Brenda Fernandes no Controle do Workspot que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Controle de Espaço de Trabalho.

**Para configurar o logon único do Azure AD com o Controle do Workspot, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Controle do Workspot**, selecione **Conexão Única**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Selecionar um método de conexão única**, selecione o modo **SAML** para ativar o logon único.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir, se desejar configurar o aplicativo no modo **IDP**:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Workspot Control](mailto:support@workspot.com) para obter esses valores. 

5. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do certificado **(Base64)** e salve no seu computador.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Na seção **Configurar o controle do workspot**, copie o URL apropriado de acordo com sua exigência.

    Observe que a URL pode dizer o seguinte:

     a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. Em uma janela diferente do navegador da Web, efetue login no Workspot Control como um administrador de segurança.

8. Na barra de ferramentas no topo da página, clique em  **Configuração** e navegue até  **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Sobre o **configuração de linguagem de marcação de asserção de segurança**, execute as seguintes etapas:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

     a. Na caixa de texto **ID da Entidade**, cole o valor de **Identificador de Anúncio do Azure** que você copiou do portal do Azure.   

    b.Na caixa de texto **URL do Serviço de Conexão**, cole o valor do **URL de Login** que você copiou do portal do Azure.

    c. Na caixa de texto **URL do serviço de logout**, cole o valor de **URL de logout** copiado do portal do Azure. 

    d. Clique no botão **arquivo de atualização** fazer o download base 64 codificados certificado que você baixou do portal do Azure, em que o certificado x. 509.

    e. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-workspot-control-test-user"></a>Criar um usuário de teste do controle Workspot

Para permitir que os usuários do AD do Azure façam logon no Controle do Workspot, eles devem ser provisionados no Controle do Workspot. No Controle do Workspot, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça o login no Workspot Control como um administrador de segurança.

2. Na barra de ferramentas no topo da página, clique em  **Usuários** e navegue até  **Adicionar Usuário**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Sobre **adicionar um novo usuário**, execute as seguintes etapas:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

     a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na **Sobrenome** texto, digite o sobrenome do usuário, como **simon**.

    c. Na caixa de texto **E-mail**, insira o e-mail do usuário como**Brittasimon@contoso.com**.

    d. Selecione a função de usuário apropriada na lista suspensa **Função**.

    e. Selecione o grupo de usuários apropriado na lista suspensa **Grupo**.

    f. Clique em **Adicionar Usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Britta Simon use o logon único do Azure, concedendo acesso ao Controle do Workspot.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Workspot controle**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Controle do Workspot no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Controle de Área de Trabalho.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
