---
title: 'Tutorial: Integração do Azure Active Directory ao LINE WORKS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LINE WORKS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 0437bd46537d8b7024618706aba12660abca5512
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414685"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Tutorial: Integração do Azure Active Directory ao LINE WORKS

Neste tutorial, você aprende a integrar o LINE WORKS ao Azure Active Directory (Azure AD).

A integração do LINE WORKS ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao LINE WORKS.
- Você pode permitir que seus usuários façam logon automaticamente no LINE WORKS (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LINE WORKS, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do LINE WORKS com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o LINE WORKS da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-line-works-from-the-gallery"></a>Adicionar o LINE WORKS da galeria
Para configurar a integração do LINE WORKS ao Azure AD, você precisará adicionar o LINE WORKS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LINE WORKS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/worksmobile-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/worksmobile-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/worksmobile-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **LINE WORKS**, selecione **LINE WORKS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o LINE WORKS com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LINE WORKS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LINE WORKS.

Para configurar e testar o logon único do Azure AD com o LINE WORKS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do LINE WORKS](#create-a-line-works-test-user)** – para ter um equivalente de Brenda Fernandes no LINE WORKS vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo LINE WORKS.

**Para configurar o logon único do Azure AD com o LINE WORKS, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **LINE WORKS**, clique em **Logon único**.

    ![image](./media/worksmobile-tutorial/b1_b2_select_sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/worksmobile-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/worksmobile-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://auth.worksmobile.com/d/login/<domain>/?userId=<ID@domain>`

    b. Na caixa de texto **Identificador**, digite uma URL: `worksmobile.com`

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

5. Na página **Configurar logon único com SAML**, na seção **Certificado de autenticação SAML**, clique em **Download** para baixar o **Certificado (bruto)** e salve-o no seu computador.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_certficate.png)

6. Na seção **Configurar LINE WORKS**, copie a URL apropriada, de acordo com seus requisitos.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![image](./media/worksmobile-tutorial/d1_samlsonfigure.png) 

7. Para configurar o logon único no **LINE WORKS**, leia os [documentos de SSO do LINE WORKS](https://developers.worksmobile.com/jp/document/1001080101) e defina uma configuração do LINE WORKS.

> [!NOTE]
> É necessário converter o arquivo de certificado baixado de .cert para .pem


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/worksmobile-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/worksmobile-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/worksmobile-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-line-works-test-user"></a>Criar um usuário de teste do LINE WORKS

Nesta seção, você criará uma usuária chamada Brenda Fernandes no LINE WORKS. Acesse a [página de administração do LINE WORKS](https://admin.worksmobile.com) e adicione os usuários na plataforma do LINE WORKS.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao LINE WORKS.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/worksmobile-tutorial/d_all_applications.png)

2. Na lista de aplicativos, escolha **LINE WORKS**.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/worksmobile-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/worksmobile-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco LINE WORKS no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo LINE WORKS.
Para saber mais sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


