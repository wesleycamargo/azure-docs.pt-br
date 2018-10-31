---
title: 'Tutorial: Integração do Azure Active Directory com o GetThere | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432006"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Tutorial: Integração do Azure Active Directory com o GetThere

Neste tutorial, você aprenderá a integrar o GetThere ao Azure Active Directory (Azure AD).

A integração do GetThere ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao GetThere.
- Você pode permitir que seus usuários façam logon automaticamente no GetThere (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GetThere, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do GetThere

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do GetThere da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-getthere-from-the-gallery"></a>Adição do GetThere da galeria
Para configurar a integração do GetThere ao Azure AD, você precisará adicionar o GetThere da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GetThere da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/getthere-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **GetThere**, escolha **GetThere** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o GetThere, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do GetThere é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GetThere.

Para configurar e testar o logon único do Azure AD com o GetThere, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do GetThere](#create-a-getthere-test-user)** - para ter um equivalente de Brenda Fernandes no GetThere vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo GetThere.

**Para configurar o logon único do Azure AD com o GetThere, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **GetThere**, escolha **Logon único**.

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Escolher um método de logon único**, escolha o modo **SAML** para ativar o logon único.

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. Na caixa de texto **Identificador**, digite uma das URLs abaixo:
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. Na caixa de texto **URL de resposta**, digite uma das URLs abaixo:
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. O aplicativo GetThere espera o valor exclusivo **Username** na declaração de nome de usuário. O cliente pode mapear o valor correto para a declaração de Nome de usuário. No exemplo abaixo, mapeamos o **Nome de usuário** como **user.mail**. No entanto, você pode alterar o mapeamento de acordo com as configurações de sua organização. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | NOME |  Atributo de Origem |  Namespace |
    | ---------------| --------------- | --------------- |
    | Sitename | "Forneça os valores de acordo com sua organização" | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Nome de Usuário |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o namespace do atributo mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Salvar**.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Fazer o download** para fazer o download do certificado **(Base64)** e salve no seu computador.

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. Na seção **Configurar o GetThere**, copie a URL apropriada, de acordo com suas necessidades.

    Observe que a URL pode dizer o seguinte:

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. Para configurar o logon único no lado do **GetThere**, você precisa enviar o **Certificado (Base64)** baixado e a **URL de logon, Identificador do Azure Ad, URL de logout** para a [Equipe de suporte ao cliente do GetThere](mailto:dataintegration@sabre.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. Escolha **Novo usuário** na parte superior da tela.

    ![image](./media/getthere-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Escolha **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-getthere-test-user"></a>Criar um usuário de teste do GetThere

Nesta seção, você criará uma usuária chamada Brenda Fernandes no GetThere. Trabalhe com a [equipe de suporte ao cliente do GetThere](mailto:dataintegration@sabre.com) para adicionar os usuários à plataforma GetThere. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao GetThere.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. Na lista de aplicativos, escolha **GetThere**.

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. Marque o botão **Adicionar** e, em seguida, escolha **Usuários e grupos** no diálogo **Adicionar Atribuição**.

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. No diálogo **Adicionar Atribuição**, escolha o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GetThere no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo GetThere.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
