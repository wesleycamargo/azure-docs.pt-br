---
title: 'Tutorial: integração do Azure Active Directory ao UserEcho | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ecdd37db662c6861e35f80bfbf4ac8ff7e0d08c9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443255"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: Integração do Active Directory do Azure com o UserEcho

Neste tutorial, você aprenderá como integrar o UserEcho ao Azure AD (Azure Active Directory).

A integração do UserEcho ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao UserEcho
- Você pode permitir que seus usuários façam logon automaticamente no UserEcho (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao UserEcho, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do UserEcho habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o UserEcho da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-userecho-from-the-gallery"></a>Adicionando o UserEcho da galeria
Para configurar a integração do UserEcho ao AD do Azure, você precisará adicionar o UserEcho da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o UserEcho da galeria, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **UserEcho**.

    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/tutorial_userecho_search.png)

1. No painel de resultados, selecione **UserEcho** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o UserEcho com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do UserEcho é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no UserEcho.

No UserEcho, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o UserEcho, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do UserEcho](#creating-a-userecho-test-user)** – para ter um equivalente de Brenda Fernandes no UserEcho que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo UserEcho.

**Para configurar o logon único do AD do Azure com o UserEcho, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **UserEcho**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. Na seção **URLs e Domínio do UserEcho**, siga as etapas abaixo:

    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.userecho.com/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do UserEcho](https://feedback.userecho.com/) para obter esses valores. 

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/userecho-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do UserEcho**, clique em **Configurar UserEcho** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. Em outra janela do navegador, entre em seu site de empresa do UserEcho como administrador.

1. Na barra de ferramentas na parte superior, clique em seu nome de usuário para expandir o menu e clique em **Instalação**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. Clique em **Integrações**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. Clique em **site** e em **Logon único (SAML2)**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. Na página **Logon Único (SAML)** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Para **Habilitado para SAML**, selecione **Sim**.
    
    b. Cole a **URL do Serviço de Logon Único SAML** copiada do Portal do Azure na caixa de texto **URL de SSO de SAML**.
    
    c. Cole a **URL de Saída** copiada no Portal do Azure para a caixa de texto **URL de Logoff remoto**.
    
    d. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado X.509** .
    
    e. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/userecho-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-userecho-test-user"></a>Criando um usuário de teste do UserEcho

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no UserEcho.

**Para criar um usuário chamado Brenda Fernandes no UserEcho, execute as seguintes etapas:**

1. Faça logon no site da empresa do UserEcho como um administrador.

1. Na barra de ferramentas na parte superior, clique em seu nome de usuário para expandir o menu e clique em **Instalação**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_06.png)

1. Clique em **Usuários** para expandir a seção **Usuários**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_10.png)

1. Clique em **Usuários**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_11.png)

1. Clique em **Convidar um novo usuário**.
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_12.png)

1. Na caixa de diálogo **Convidar novo usuário** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como Brenda Fernandes.
    
    b.  Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.
    
    c. Clique em **Convidar**.

Um convite é enviado para Brenda, que permite que ela começar a usar o UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao UserEcho.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao UserEcho, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **UserEcho**.

    ![Configurar o logon único](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando você clicar no bloco UserEcho no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo UserEcho.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

