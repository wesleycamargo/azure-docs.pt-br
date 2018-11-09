---
title: 'Tutorial: integração do Azure Active Directory ao MyWorkDrive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094049"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Tutorial: integração do Azure Active Directory ao MyWorkDrive

Neste tutorial, você aprenderá como integrar o MyWorkDrive ao Microsoft Azure AD (Azure Active Directory).

A integração do MyWorkDrive ao Microsoft Azure AD oferece os seguintes benefícios:

- É possível controlar no Microsoft Azure AD quem tem acesso ao MyWorkDrive.
- É possível permitir que seus usuários façam logon automaticamente no MyWorkDrive (logon único) com as contas do Microsoft Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao MyWorkDrive, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura com logon único do MyWorkDrive habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o MyWorkDrive da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionar o MyWorkDrive da galeria
Para configurar a integração do MyWorkDrive ao Microsoft Azure AD, será necessário adicionar o MyWorkDrive por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MyWorkDrive por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/myworkdrive-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/myworkdrive-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **MyWorkDrive**, selecione **MyWorkDrive** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o MyWorkDrive, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do MyWorkDrive é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no MyWorkDrive.

Para configurar e testar o logon único do Microsoft Azure AD com o MyWorkDrive, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do MyWorkDrive](#create-a-myworkdrive-test-user)** – para ter um equivalente de Brenda Fernandes no MyWorkDrive que esteja vinculado à representação de usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal do Azure e configurará o logon único no aplicativo do MyWorkDrive.

**Para configurar o logon único do Microsoft Azure AD com o MyWorkDrive, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **MyWorkDrive**, selecione **Logon único**.

    ![image](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Selecionar um método de conexão única**, selecione o modo **SAML** para ativar o logon único.

    ![image](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Clique em **Defina URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon.  Insira o nome do host do MyWorkDrive Server da sua empresa: por exemplo,
    > 
    > URL de Resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de logon:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Entre em contato com a equipe de suporte ao cliente MyWorkDrive se você não tiver certeza de como configurar seu próprio nome de host e certificado SSL para esses valores.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique no **ícone** para copiar **URL de metadados da federação do aplicativo** e clique em **Faça o download** para baixar o **Certificado (Base64)** salve no seu computador.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. Na seção **Configurar o MyWorkDrive**, copie o URL apropriado de acordo com sua exigência.

    Observe que a URL pode dizer o seguinte:

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

    ![image](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Para configurar o logon único no lado do MyWorkDrive, faça o download do **Certificado (Base64), URL de Saída, ID da Entidade SAML e URL do Serviço de Logon Único SAML** e configure-os manualmente no servidor MyWorkDrive ou copie e copie cole o URL de metadados de federação do **aplicativo do Azure** na tela de configuração do SAML Azure AD do painel de administração do servidor MyWorkDrive. Para obter informações adicionais, entre em contato com a [equipe de suporte do MyWorkDrive](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/myworkdrive-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/myworkdrive-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-myworkdrive-test-user"></a>Criar um usuário de teste do MyWorkDrive

Nesta seção, você criará uma usuária chamada Brenda Fernandes no MyWorkDrive. Trabalhe com a equipe de suporte  [MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os usuários à plataforma MyWorkDrive. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao MyWorkDrive.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/myworkdrive-tutorial/d_all_applications.png)

2. Na lista de aplicativos, escolha **MyWorkDrive**.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/myworkdrive-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MyWorkDrive no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo MyWorkDrive.
Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
