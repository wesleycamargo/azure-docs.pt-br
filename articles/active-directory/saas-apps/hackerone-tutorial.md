---
title: 'Tutorial: Integração do Azure Active Directory ao Hackerone | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HackerOne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: fe070505970516efcd4e2ae46dedff2792f95b08
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817190"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração do Azure Active Directory ao HackerOne

Neste tutorial, você aprenderá a integrar o HackerOne ao Azure AD (Azure Active Directory).

A integração do HackerOne ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao HackerOne
- Você pode permitir que seus usuários façam logon automaticamente no HackerOne (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HackerOne, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do HackerOne

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o HackerOne da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-hackerone-from-the-gallery"></a>Adicionando o HackerOne da galeria
Para configurar a integração do HackerOne ao Azure AD, você precisará adicionar o HackerOne da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HackerOne da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **HackerOne**.

    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/tutorial_hackerone_search.png)

1. No painel de resultados, selecione **HackerOne** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o HackerOne, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do HackerOne é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HackerOne.

No HackerOne, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o HackerOne, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do HackerOne](#creating-a-hackerone-test-user)**: para ter um equivalente de Brenda Fernandes no HackerOne que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo HackerOne.

**Para configurar o logon único do Azure AD com o HackerOne, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **HackerOne**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_samlbase.png)

1. Sobre a **URL de Logon único do HackerOne e o Identificador**, execute as seguintes etapas:

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://hackerone.com/<company name>/authentication`

    b. Na caixa de texto **Identificador**, digite uma URL como:  `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Para obter esse valor, entre em contato com a [equipe de suporte do HackerOne](mailto:support@hackerone.com). 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do HackerOne**, clique em **Configurar HackerOne** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_configure.png) 

1. Faça logon no seu locatário do HackerOne como administrador.

1. Na parte superior do menu, clique em “**Configurações**”.
   
    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_001.png) 

1. Navegue até "**Autenticação**" e clique em "**Adicionar configurações do SAML**".
   
    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_003.png) 

1. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_004.png) 

     a. Na caixa de texto **Domínio de Email** , digite um domínio registrado.

    b. Nas caixas de texto   **URL de Logon Único**, cole o valor da  **URL do Serviço de Logon Único do SAML**  copiado no portal do Azure.

    c. Abra o seu **Arquivo de certificado** baixado do portal do Azure no bloco de notas, copie o conteúdo para a área de transferência e cole-o na caixa de texto **Certificado X509**  .
    
    d. Clique em **Salvar**.

1. No diálogo Configurações de Autenticação, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_005.png) 

     a. Clique em **Executar teste**.

    b. Se o valor do campo **Status** for igual a **Status do último teste: criado**, contate sua [equipe de suporte do HackerOne](mailto:support@hackerone.com) para solicitar uma análise da sua configuração.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/hackerone-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-hackerone-test-user"></a>Criação de um usuário de teste do HackerOne

Em seguida, você criará um usuário chamado Brenda Fernandes no HackerOne. O HackerOne dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Quando você acessar HackerOne, um novo usuário será criado caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará entrar em contato com a equipe de suporte do Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao HackerOne.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao HackerOne, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **HackerOne**.

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Por fim, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Quando você clica no bloco HackerOne no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo HackerOne.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hackerone-tutorial/tutorial_general_01.png
[2]: ./media/hackerone-tutorial/tutorial_general_02.png
[3]: ./media/hackerone-tutorial/tutorial_general_03.png
[4]: ./media/hackerone-tutorial/tutorial_general_04.png

[100]: ./media/hackerone-tutorial/tutorial_general_100.png

[200]: ./media/hackerone-tutorial/tutorial_general_200.png
[201]: ./media/hackerone-tutorial/tutorial_general_201.png
[202]: ./media/hackerone-tutorial/tutorial_general_202.png
[203]: ./media/hackerone-tutorial/tutorial_general_203.png

