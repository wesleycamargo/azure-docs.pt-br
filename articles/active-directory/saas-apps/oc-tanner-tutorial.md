---
title: 'Tutorial: Integração do Azure Active Directory com o O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: a2234db09ecd8d2b3f654a18017d50f43c8caca6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822290"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Tutorial: Integração do Azure Active Directory com o O.C. Tanner - AppreciateHub

Neste tutorial, você aprenderá a integrar o O.C. Tanner - AppreciateHub ao Azure Active Directory (Azure AD)

Integração do O.C. O Tanner - AppreciateHub ao AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem terá acesso ao O.C. Tanner - AppreciateHub
- Você pode permitir que seus usuários façam logon automaticamente no O.C. Tanner - AppreciateHub (Logon Único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao O.C. Tanner - AppreciateHub, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um Assinatura habilitada para logon único do Tanner – AppreciateHub

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o O.C. Tanner - AppreciateHub a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Adicionar o O.C. Tanner - AppreciateHub a partir da galeria
Para configurar a integração do O.C. Tanner - AppreciateHub no AD do Azure, você precisa adicionar O.C. Tanner - AppreciateHub a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o O.C. Tanner - AppreciateHub da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **O.C. Tanner - AppreciateHub**.

    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

1. No painel de resultados, selecione **O.C. Tanner – AppreciateHub** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, configura e testa o logon único do Azure AD com o O.C. Tanner - AppreciateHub com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual o usuário do O.C. Tanner – AppreciateHub é para um usuário no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no O.C. Tanner - o AppreciateHub precisa ser estabelecido.

No O.C. Tanner – AppreciateHub, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com O.C. Tanner - AppreciateHub, é necessário concluir os seguintes blocos de criação:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário teste do O.C. Tanner - AppreciateHub](#creating-a-oc-tanner---appreciatehub-test-user)** - ter um equivalente de Brenda Fernandes no O.C. Tanner – AppreciateHub que é vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no seu aplicativo O.C. Aplicativo Tanner - AppreciateHub.

**Para configurar o logon único do AD do Azure com O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **O.C. Tanner – AppreciateHub**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

1. Na seção **URLs e Domínio do O.C. Tanner – AppreciateHub**, execute as seguintes etapas:

    ![Configurar o logon único](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

     a. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Resposta real. Contate a [Equipe de suporte do O.C. Tanner – AppreciateHub](mailto:sso@octanner.com) para obter esse valor.

    b. Abra o arquivo de metadados usando o seguinte link: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Localize o nó **md:AssertionConsumerService** . 
   
    d. Copie o valor do atributo **Local** . 
   
    ![Definir configurações de aplicativo][12]
   
    e. Na caixa de texto **URL do Logon** , antes do valor que você obteve na etapa anterior.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/oc-tanner-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do aplicativo do **O.C. Tanner – AppreciateHub**, você precisa enviar o **XML de Metadados** baixado para a [equipe de suporte do O.C. Tanner – AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/oc-tanner-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Criar um usuário teste do O.C. Usuário de teste do Tanner - AppreciateHub

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub.

**Para criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

Peça à sua [equipe de suporte do O.C. Tanner – AppreciateHub](mailto:sso@octanner.com) para criar um usuário que tenha o atributo nameID com o mesmo valor que o nome de usuário Brenda Fernandes no Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você concederá a Brenda Fernandes acesso ao O.C. para que ela fique habilitada a usar o logon único do Azure. Tanner - AppreciateHub.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **O.C. Tanner - AppreciateHub**.

    ![Configurar o logon único](./media/oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco O.C. Tanner - AppreciateHub no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo O.C. Aplicativo Tanner - AppreciateHub.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/oc-tanner-tutorial/tutorial_general_203.png

