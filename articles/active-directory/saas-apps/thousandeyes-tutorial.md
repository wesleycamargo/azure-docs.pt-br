---
title: 'Tutorial: Integração do Azure Active Directory com o ThousandEyes | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43aa43d37099d69441a5dde47327e784d67fa9ac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189439"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Azure Active Directory com o ThousandEyes

Neste tutorial, você aprenderá a integrar o ThousandEyes ao Azure AD (Azure Active Directory).

A integração do ThousandEyes com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ThousandEyes
- É possível permitir que os usuários entrem automaticamente no ThousandEyes (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ThousandEyes, serão necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do ThousandEyes habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, é possível obter uma avaliação por um mês aqui: [Oferta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o ThousandEyes da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionar o ThousandEyes da galeria
Para configurar a integração do ThousandEyes com o Azure AD, é necessário adicionar o ThousandEyes da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ThousandEyes da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **ThousandEyes**.

    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. No painel de resultados, selecione **ThousandEyes** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o ThousandEyes, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ThousandEyes é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ThousandEyes.

No ThousandEyes, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ThousandEyes, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do ThousandEyes](#creating-a-thousandeyes-test-user)** – para ter um equivalente de Brenda Fernandes no ThousandEyes que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo ThousandEyes.

**Para configurar o logon único do Azure AD com o ThousandEyes, siga estas etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **ThousandEyes**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Na seção **URLs e Domínio do ThousandEyes**, siga estas etapas:

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://app.thousandeyes.com/login/sso`

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do ThousandEyes**, clique em **Configurar ThousandEyes** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

1. No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/thousandeyes-tutorial/ic790066.png "Configurações")

1. Clique em **Conta**

    ![Conta](./media/thousandeyes-tutorial/ic790067.png "Conta")

1. Clique na guia **Segurança e Autenticação**.

    ![Segurança e autenticação](./media/thousandeyes-tutorial/ic790068.png "Segurança e autenticação")

1. Na seção **Configurações de Logon Único** , realize as seguintes etapas:

    ![Configurar o logon único](./media/thousandeyes-tutorial/ic790069.png "Configurar o logon único")

     a. Selecione **Habilitar Logon Único**.

    b. Na caixa de texto **URL de Página de Logon**, cole a **URL do Serviço de Logon Único SAML** copiada do portal do Azure.

    c. Na caixa de texto **URL de Página de Logoff**, cole a **URL de Saída** copiada do portal do Azure.

    d. Na caixa de texto **Emissor do Provedor de Identidade**, cole a **ID de Entidade SAML** copiada do portal do Azure.

    e. Em **Certificado de Verificação**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do Portal do Azure.

    f. Clique em **Salvar**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-thousandeyes-test-user"></a>Criação de um usuário de teste do ThousandEyes

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ThousandEyes. O ThousandEyes dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar [aqui](thousandeyes-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa ThousandEyes como um administrador.

1. Clique em **Configurações**.

    ![Configurações](./media/thousandeyes-tutorial/IC790066.png "Configurações")

1. Clique em **Conta**.

    ![Conta](./media/thousandeyes-tutorial/IC790067.png "Conta")

1. Clique na guia **Contas e Usuários**.

    ![Contas e usuários](./media/thousandeyes-tutorial/IC790073.png "Contas e usuários")

1. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:

    ![Adicionar contas de usuário](./media/thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")

     a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda Fernandes**.

    b. Na caixa de texto **Email**, digite o email do usuário como **brittasimon@contoso.com**.

    b. Clique em **Adicionar Novo Usuário à Conta**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory receberá um email com um link para confirmar e ativar a conta.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao ThousandEyes.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ThousandEyes, siga estas etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **ThousandEyes**.

    ![Configurar o logon único](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco ThousandEyes no Painel de Acesso, você deverá entrará automaticamente no seu aplicativo ThousandEyes.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
