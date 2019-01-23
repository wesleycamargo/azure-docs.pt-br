---
title: 'Tutorial: Integração do Azure Active Directory com o Tableau Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 10b82a6c51f8fc31b5b6f4b8c661f764fc6b309d
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198823"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integração do Azure Active Directory ao Tableau Online

Neste tutorial, você aprenderá a integrar o Tableau Online ao Azure AD (Azure Active Directory).

A integração do Tableau Online ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Tableau Online
- Você pode habilitar os usuários a entrar automaticamente no Tableau Online (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Tableau Online, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Tableau Online

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Tableau Online da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-tableau-online-from-the-gallery"></a>Adição do Tableau Online da galeria
Para configurar a integração do Tableau Online ao Azure AD, você precisa adicionar o Tableau Online da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Online por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Tableau Online**.

    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. No painel de resultados, selecione **Tableau Online** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Tableau Online, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tableau Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Online.

No Tableau Online, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Tableau Online, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Tableau Online](#creating-a-tableau-online-test-user)** – para ter um equivalente de Brenda Fernandes no Tableau Online que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Tableau Online.

**Para configurar o logon único do Azure AD com o Tableau Online, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Tableau Online**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. Na seção **Domínio e URLs do Tableau Online**, realize as seguintes etapas:

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
     a. Na caixa de texto **URL de Logon**, digite a URL: `https://sso.online.tableau.com`

    b. Na caixa de texto **Identificador**, digite a URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_general_400.png)

1. Em uma janela de navegador diferente, entre no seu aplicativo Tableau Online. Vá para **Settings** e para **Authentication**.
   
    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. Para habilitar SAML, na seção **Tipos de Autenticação**. Marque a caixa de seleção **Logon Único com SAML**.
   
    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Role para baixo até a seção **Import metadata file into Tableau Online** .  Clique em Browser e importe o arquivo de metadados que você baixou do Azure AD. Em seguida, clique em **Apply**.
   
   ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. Na seção **Corresponder Instruções de Declaração**, insira o nome de instrução de declaração do Provedor de Identidade correspondente para **endereço de email**, **nome** e **sobrenome**. Para obter essas informações do Azure AD: 
  
     a. No portal do Azure, acesse a página de integração de aplicativos do **Tableau Online**.
    
    b. Na seção Atributos, marque a caixa de seleção **“Exibir e editar todos os outros atributos de usuário”**. 
    
   ![Configurar o logon único](./media/tableauonline-tutorial/attributesection.png)
      
    c. Copie o valor de namespace para esses atributos: nome, email e sobrenome usando as seguintes etapas:

   ![Logon Único do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Clique no valor **user.givenname** 
    
    e. Copie o valor da caixa de texto **namespace**.

   ![Configurar o logon único](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores de namespace para o email e o sobrenome, siga as etapas anteriores.

    g. Alterne para o aplicativo Tableau Online e defina a seção **Atributos do Tableau Online** como a seguir:
     * Email: **mail** ou **userprincipalname**
     * Nome: **givenname**
     * Sobrenome: **surname**
   
   ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-tableau-online-test-user"></a>Criação de um usuário de teste do Tableau Online

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tableau Online.

1. No **Tableau Online**, clique em **Configurações** e na seção **Autenticação**. Role para baixo até a seção **Selecionar Usuários** . Clique em **Adicionar Usuários** e em **Inserir Endereços de Email**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Selecione **Adicionar usuários para autenticação de logon único (SSO)**. Na caixa de texto **Inserir Endereços de Email**, adicione britta.simon@contoso.com
   
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tableau Online.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tableau Online, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Tableau Online**.

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tableau Online no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Tableau Online.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png
