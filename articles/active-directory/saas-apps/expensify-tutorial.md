---
title: 'Tutorial: Integração do Azure Active Directory com o Expensify | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: 5d61ac27eb5a6e4f546d8c6db66b84f2c204b507
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840462"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Tutorial: Integração do Azure Active Directory com o Expensify

Neste tutorial, você aprenderá como integrar o Expensify com o Azure Active Directory (Azure AD).

A integração do Expensify com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Expensify.
- Você pode permitir que seus usuários façam logon automaticamente no Expensify (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Expensify, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Expensify

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Expensify da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-expensify-from-the-gallery"></a>Adicionando o Expensify da galeria

Para configurar a integração do Expensify ao Azure AD, você precisa adicionar o Expensify da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Expensify da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Expensify**, selecione **Expensify** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Expensify, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Expensify é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Expensify.

No Expensify, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Expensify, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Expensify](#create-an-expensify-test-user)** – para ter um equivalente de Brenda Fernandes no Expensify que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Expensify.

**Para configurar o logon único do Azure AD com o Expensify, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Expensify**, selecione **Logon único**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

     a. Na caixa de texto **URL de Entrada**, digite uma URL como: `https://www.expensify.com/authentication/saml/login`

    b. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Substitua a seção <companyname> da URL do Identificador pelo domínio da sua empresa. Veja o exemplo de `https://contoso.expensify.com` acima. Em Expensify, esse é o nome do seu domínio, conforme mostrado em **Configurações > Controle de Domínio**.

    ![Informações do domínio do Expensify](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o certificado apropriado de acordo com seus requisitos e salve-o no computador.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Para habilitar o SSO no Expensify, primeiro habilite o **Controle de Domínio** no aplicativo. Você pode habilitar o Controle de Domínio no aplicativo por meio das etapas listadas [aqui](https://help.expensify.com/domain-control). Para obter suporte adicional, trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com). Depois que o Controle de Domínio estiver habilitado, siga estas etapas:
   
    ![Configurar o logon único](./media/expensify-tutorial/tutorial_expensify_51.png)
    
     a. Faça logon no seu aplicativo Expensify.
    
    b. No painel esquerdo, clique em **Configurações** e navegue para **SAML**.
    
    c. Alterne a opção **Logon do SAML** para **habilitado**.
    
    d. Abra os Metadados de Federação baixados do Azure AD no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/expensify-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-an-expensify-test-user"></a>Criar um usuário de teste do Expensify

Nesta seção, você criará um usuário chamado Brenda Fernandes no Expensify. Trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com) para adicionar usuários na plataforma do Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Expensify.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Expensify no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Expensify.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)




