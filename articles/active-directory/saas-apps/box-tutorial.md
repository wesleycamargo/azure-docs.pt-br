---
title: 'Tutorial: integração do Azure Active Directory com o Box | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 535f59b1b0dc56b183c8a019d101b4fd4f1bfad6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116118"
---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Integração do Active Directory do Azure ao Box

Neste tutorial, você aprenderá a integrar o Box ao Azure AD (Azure Active Directory).

A integração do Box ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Box.
- Você pode permitir que seus usuários façam logon automaticamente no Box (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Box, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para logon único do Box

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Box da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-box-from-the-gallery"></a>Adicionar o Box da galeria
Para configurar a integração do Box ao Azure AD, você precisa adicionar o Box por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Box por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/box-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/box-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/box-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Box**, selecione **Box** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/box-tutorial/tutorial_Box_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Box com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Box é equivalente a um determinado usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Box.

Para configurar e testar o logon único do Azure AD com o Box, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Box](#create-a-box-test-user)** – para ter um equivalente de Brenda Fernandes no Box que esteja vinculado à sua representação no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Box.

**Para configurar o logon único do Azure AD com o Box, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Caixa**, selecione **Conexão Única**.

    ![image](./media/box-tutorial/b1_b2_select_sso.png)

2. Na caixa de diálogo **Selecionar um método de conexão única**, clique em **Selecione** para o modo **SAML** para ativar o logon único.

    ![image](./media/box-tutorial/b1_b2_saml_sso.png)

3. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![image](./media/box-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, execute as seguintes etapas:

    a. Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.account.box.com`

    b. Na caixa de texto **identificador**, digite uma URL:`box.net`

    ![image](./media/box-tutorial/tutorial_box_url.png)

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte do Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) para obter o valor.
 
5. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **XML de metadados de federação** e salve-o no seu computador.

    ![image](./media/box-tutorial/tutorial_Box_certificate.png)

6. Para configurar o SSO para seu aplicativo, siga o procedimento em [Configurar SSO por conta própria](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown). 

>[!NOTE]
>Se você não conseguir definir as configurações de SSO para sua conta do Box, será necessário enviar o **XML de metadados de federação** para [equipe de suporte do Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Active Directory do Azure**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![image](./media/box-tutorial/d_users_and_groups.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![image](./media/box-tutorial/d_adduser.png)

3. Nas propriedades do usuário, execute as seguintes etapas.

    ![image](./media/box-tutorial/d_userproperties.png)

    a. No **nome** Inserir campo **Brendafernandes**.
  
    b. No **nome de usuário** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-box-test-user"></a>Criar um usuário de teste do Box

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Box. Caixa dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar a caixa se ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com [equipe de suporte do Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Box.

1. No portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**.

    ![image](./media/box-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione o **Box**.

    ![image](./media/box-tutorial/tutorial_Box_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/box-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar atribuição**.

    ![image](./media/box-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. No diálogo **Adicionar atribuição**, selecione o **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você seleciona o bloco **Caixa** no Painel de Acesso, você obtém a página de entrada para fazer login no aplicativo Caixa.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar provisionamento de usuário](box-userprovisioning-tutorial.md)


