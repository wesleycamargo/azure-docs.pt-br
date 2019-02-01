---
title: 'Tutorial: Integração do Azure Active Directory ao FM:Systems | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FM:Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: a1d12784fb3f88889cd9ba0d622d1bc5d84374a8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165329"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Tutorial: Integração do Azure Active Directory ao FM:Systems

Neste tutorial, você aprenderá a integrar o FM:Systems ao Azure AD (Azure Active Directory).

A integração do FM:Systems ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao FM:Systems
- Você pode permitir que seus usuários entrem automaticamente no FM:Systems (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FM:Systems, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do FM:Systems

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando FM:Systems da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-fmsystems-from-the-gallery"></a>Adicionando FM:Systems da Galeria
Para configurar a integração do FM:Systems ao Azure AD, você precisará adicionar o FM:Systems da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FM:Systems da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **FM:Systems**.

    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/tutorial_fmsystems_search.png)

1. No painel de resultados, selecione **FM:Systems** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/tutorial_fmsystems_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o FM:Systems com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FM:Systems é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FM:Systems.

No FM:Systems, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o FM:Systems, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Como criar um usuário de teste do FM:Systems](#creating-an-fmsystems-test-user)** – para ter um equivalente de Brenda Fernandes no FM:Systems que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo FM:Systems.

**Para configurar o logon único do Azure AD com o FM:Systems, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **FM:Systems**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/fm-systems-tutorial/tutorial_fmsystems_samlbase.png)

1. Na seção **URLs e Domínio do FM:Systems**, execute as seguintes etapas:

    ![Configurar o logon único](./media/fm-systems-tutorial/tutorial_fmsystems_url.png)

    Na caixa de texto **URL de resposta**, digite a **URL de resposta** do FM:Systems usando o seguinte padrão: `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Resposta real. Entre em contato com [equipe de suporte do FM:Systems](https://fmsystems.com/ask-us/) para obter esse valor.
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/fm-systems-tutorial/tutorial_fmsystems_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/fm-systems-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **FM:Systems**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do FM:Systems](https://fmsystems.com/ask-us/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/fm-systems-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-fmsystems-test-user"></a>Criar um usuário de teste do FM:Systems

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do FM:Systems como administrador.

1. Vá para **Administração do Sistema \> Gerenciar Segurança \> Usuários \> Lista de usuários**.
   
    ![Administração do Sistema](./media/fm-systems-tutorial/ic795905.png "Administração do Sistema")

1. Clique em **Criar novo usuário**.
   
    ![Criar Novo Usuário](./media/fm-systems-tutorial/ic795906.png "Criar Novo Usuário")

1. Na seção **Criar Usuário** , realize as seguintes etapas:
   
    ![Criar usuário](./media/fm-systems-tutorial/ic795907.png "Criar usuário")
   
     a. Digite o **Nome de Usuário**, a **Senha**, **Confirmar Senha**, o **Email** e a **ID do Funcionário** de uma conta válida do Azure Active Directory que você deseje provisionar nas caixas de texto relacionadas.
   
    b. Clique em **Próximo**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao FM:Systems.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao FM:Systems, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **FM:Systems**.

    ![Configurar o logon único](./media/fm-systems-tutorial/tutorial_fmsystems_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco FM:Systems no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo FM:Systems.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fm-systems-tutorial/tutorial_general_01.png
[2]: ./media/fm-systems-tutorial/tutorial_general_02.png
[3]: ./media/fm-systems-tutorial/tutorial_general_03.png
[4]: ./media/fm-systems-tutorial/tutorial_general_04.png

[100]: ./media/fm-systems-tutorial/tutorial_general_100.png

[200]: ./media/fm-systems-tutorial/tutorial_general_200.png
[201]: ./media/fm-systems-tutorial/tutorial_general_201.png
[202]: ./media/fm-systems-tutorial/tutorial_general_202.png
[203]: ./media/fm-systems-tutorial/tutorial_general_203.png

