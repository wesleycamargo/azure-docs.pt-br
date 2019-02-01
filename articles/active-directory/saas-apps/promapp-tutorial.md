---
title: 'Tutorial: Integração do Azure Active Directory ao Promapp | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: d2fbcc5dcf97eeb63c058ca49260d50c815bd14d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184233"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integração do Active Directory do Azure com o Promapp

Neste tutorial, você aprende a integrar o Promapp ao Azure AD (Azure Active Directory).

A integração do Promapp ao Azure AD proporciona os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Promapp
- Você pode habilitar seus usuários a fazerem logon automaticamente no Promapp (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Promapp, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Promapp

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Promapp da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-promapp-from-the-gallery"></a>Adicionando Promapp da galeria
Para configurar a integração do Promapp ao Azure AD, você precisa adicionar o Promapp por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Azure AD por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Promapp**.

    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/tutorial_promapp_search.png)

1. No painel de resultados, selecione **Promapp** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Promapp, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Promapp é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Promapp.

No Promapp, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Promapp, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Promapp](#creating-a-promapp-test-user)** – para ter um equivalente de Brenda Fernandes no Promapp que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Promapp.

**Para configurar o logon único do Azure AD com o Promapp, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Promapp**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_samlbase.png)

1. Na seção **Domínio e URLs Promapp**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > Atualmente a integração do Azure AD com Promapp só foi configurada para autenticação iniciadas, por exemplo, ir para uma URL Promapp inicia o processo de autenticação. No entanto, a URL de resposta é um campo obrigatório.
    
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do Promapp](https://www.promapp.com/about-us/contact-us/) para obter esses valores.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Promapp**, clique em **Configurar o Promapp** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_configure.png) 

1. Faça logon no site da empresa Promapp como administrador. 

1. No menu na parte superior, clique em **Administrador**. 
   
    ![Logon Único do AD do Azure][12]

1. Clique em **Configurar**. 
   
    ![Logon Único do AD do Azure][13]

1. No diálogo **Segurança** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][14]
    
     a. Cole a **URL do Serviço de Logon Único SAML** copiada do portal do Azure na caixa de texto **URL de Logon SSO**.
    
    b. Para **SSO - Modo Logon Único**, selecione **Opcional** e clique em **Salvar**.

    > [!NOTE]
    > O modo **Opcional** é destinado somente a teste. Quando estiver satisfeito com a configuração, selecione o modo **Necessário** para forçar todos os usuários a autenticarem-se usando o Azure AD.

    c. Abra o certificado baixado no bloco de notas, copie o conteúdo do certificado, sem a primeira linha (-----**BEGIN CERTIFICATE**-----) e a última linha (-----**END CERTIFICATE**-----), cole-o na caixa de texto **Certificado SSO-x.509** e clique em **Salvar**.
        
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/promapp-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-promapp-test-user"></a>Criar um usuário de teste Promapp

O aplicativo Promapp dá suporte ao provisionamento Just-in-Time. Isso significa que uma conte de usuário será criada automaticamente se necessário durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Promapp.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Promapp, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Promapp**.

    ![Configurar o logon único](./media/promapp-tutorial/tutorial_promapp_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Para testar seu aplicativo no modo **SP** iniciado, você precisará iniciar a autenticação do seu site Promapp. Isso pode ser feito clicando no botão “Entrar com logon único” na página de logon enquanto o modo **Opcional** modo estiver habilitado.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promapp-tutorial/tutorial_general_01.png
[2]: ./media/promapp-tutorial/tutorial_general_02.png
[3]: ./media/promapp-tutorial/tutorial_general_03.png
[4]: ./media/promapp-tutorial/tutorial_general_04.png
[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/promapp-tutorial/tutorial_general_100.png

[200]: ./media/promapp-tutorial/tutorial_general_200.png
[201]: ./media/promapp-tutorial/tutorial_general_201.png
[202]: ./media/promapp-tutorial/tutorial_general_202.png
[203]: ./media/promapp-tutorial/tutorial_general_203.png

