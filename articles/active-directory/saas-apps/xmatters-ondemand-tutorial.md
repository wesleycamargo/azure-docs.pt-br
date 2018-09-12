---
title: 'Tutorial: integração do Azure Active Directory com o xMatters OnDemand | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: a235b85887e64e0a5ca35aae8f31734250a78bb5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160227"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: integração do Azure Active Directory com o xMatters OnDemand

Neste tutorial, você aprenderá como integrar o xMatters OnDemand ao Azure Active Directory (Azure AD).

A integração do xMatters OnDemand ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao xMatters OnDemand
- É possível permitir que os usuários entrem automaticamente no xMatters OnDemand (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o xMatters OnDemand, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do xMatters OnDemand habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o xMatters OnDemand da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar o xMatters OnDemand da galeria
Para configurar a integração do xMatters OnDemand com o Azure AD, é necessário adicionar o xMatters OnDemand da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o xMatters OnDemand da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **xMatters OnDemand**.

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. No painel de resultados, selecione **xMatters OnDemand** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o xMatters OnDemand com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do xMatters OnDemand é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no xMatters OnDemand.

No xMatters OnDemand, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o xMatters OnDemand, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do xMatters OnDemand](#creating-a-xmatters-ondemand-test-user)** – para ter um equivalente de Brenda Fernandes no xMatters OnDemand vinculado à representação de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo xMatters OnDemand.

**Para configurar o logon único do Azure AD com o xMatters OnDemand, siga as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **xMatters OnDemand**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. Na seção **URLs e Domínio do xMatters OnDemand**, siga as etapas abaixo:

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate [equipe de suporte do xMatters OnDemand](https://www.xmatters.com/company/contact-us/) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado localmente como **c:\\XMatters OnDemand.cer**.

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > É necessário encaminhar o certificado para a [equipe de suporte do xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado precisa ser carregado pela equipe de suporte do xMatters antes de você finalizar a configuração de logon único. 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do xMatters OnDemand**, clique em **Configurar o xMatters OnDemand** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa XMatters OnDemand como administrador.

1. Na barra de ferramentas na parte superior, clique em **Administrador** e em **Detalhes da Empres**a na barra de navegação à esquerda.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

1. Na página **Configuração do SAML** , realize as seguintes etapas:

    ![Configuração SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Configuração SAML")

    a. Selecione **Habilitar SAML**.

    b. Na caixa de texto **Identidade do provedor de identidade**, cole o **valor da ID de entidade SAML** que você copiou do portal do Azure.

    c. Na caixa de texto **Single Sign On URL**, cole **o URL do Serviço de Logon Único SAML** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logout único**, cole **URL de log-in**, copiado do portal do Azure.

    e. Na página Detalhes da Empresa, na parte superior, clique em **Salvar Alterações**.

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Criação de um usuário de teste do xMatters OnDemand

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no xMatters OnDemand.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu locatário do **XMatters OnDemand** .

1.  Clique na guia **Usuários** e, em seguida, **Adicionar Usuário**.

    ![Usuários](./media/xmatters-ondemand-tutorial/IC781048.png "Usuários")

1. Na seção **Adicionar um Usuário** , realize as seguintes etapas:

    ![Adicionar um Usuário](./media/xmatters-ondemand-tutorial/IC781049.png "Adicionar um Usuário")

    a. Selecione **Ativo**.

    b. Na caixa de texto **ID de usuário**, digite a ID de usuário, como Brittasimon@contoso.com.

    c. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    d. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    e. Na caixa de texto **Site**, insira o site válido de uma conta válida do Azure AD que você deseja provisionar.

    f. Clique em **Salvar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao xMatters OnDemand.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao xMatters OnDemand, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **xMatters OnDemand**.

    ![Configurar o logon único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco xMatters OnDemand no Painel de Acesso, você deverá entrar automaticamente em seu aplicativo xMatters OnDemand.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
