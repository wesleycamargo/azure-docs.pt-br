---
title: 'Tutorial: Integração do Azure Active Directory ao Syncplicity | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 657373d1b9ee7e66ffca495bba598c11bd3f3933
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807554"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Azure Active Directory ao Syncplicity

Neste tutorial, você aprenderá a integrar o Syncplicity ao Azure AD (Azure Active Directory).

A integração do Syncplicity ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Syncplicity
- Você pode permitir que seus usuários façam logon automaticamente no Syncplicity (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Syncplicity, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Syncplicity

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Syncplicity da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar o Syncplicity da galeria
Para configurar a integração do Syncplicity ao Azure AD, você precisará adicionar o Syncplicity da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Syncplicity da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Syncplicity**.

    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. No painel de resultados, selecione **Syncplicity** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Syncplicity, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Syncplicity é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Syncplicity.

No Syncplicity, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Syncplicity, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Syncplicity](#creating-a-syncplicity-test-user)** – para ter um equivalente de Brenda Fernandes no Syncplicity que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Syncplicity.

**Para configurar o logon único do Azure AD com o Syncplicity, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Syncplicity**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Na seção **URLs e Domínio do Syncplicity**, execute as seguintes etapas:

    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Syncplicity](https://www.syncplicity.com/contact-us) para obter esses valores. 
 

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Syncplicity**, clique em **Configurar Syncplicity** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Entre no locatário do **Syncplicity** .

1. No menu, na parte superior, clique em **administrador**, selecione **configurações** e clique em **Domínio personalizado e logon único**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na página do diálogo **SSO (Logon Único)** , realize as seguintes etapas:
   
    ![Logon Único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

     a. Na caixa de texto **Domínio Personalizado** , digite o nome do seu domínio.
  
    b. Selecione **Habilitado** como **Status do Logon Único**.

    c. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade do SAML** copiado no portal do Azure.

    d. Na caixa de texto **URL da página de entrada**, cole o valor da **URL do Serviço de Logon Único do SAML** copiado do portal do Azure.

    e. Na caixa de texto **URL da Página de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.

    f. Em **Certificado do Provedor de Identidade**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do portal do Azure. 

    g. Clique em **SALVAR ALTERAÇÕES**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-syncplicity-test-user"></a>Criar um usuário de teste do Syncplicity
Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do AAD no Syncplicity.

**Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:**

1. Faça logon no locatário do **Syncplicity** (por exemplo, `https://company.Syncplicity.com`).

1. Clique em **Administrador** e selecione **Contas de Usuário**.

1. Clique em **ADICIONAR UM USUÁRIO**.
   
    ![Gerenciar Usuários](./media/syncplicity-tutorial/ic769764.png "Gerenciar Usuários")

1. Digite os **Endereços de Email** da conta do AAD que você deseja provisionar, selecione **Usuário** como **Função** e clique em **AVANÇAR**.
   
    ![Dados da Conta](./media/syncplicity-tutorial/ic769765.png "Dados da Conta")
   
    >[!NOTE]
    >O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta. 
    > 

1. Selecione um grupo em sua empresa do qual o novo usuário deverá se tornar membro e clique em **AVANÇAR**.
   
    ![Associação a um Grupo](./media/syncplicity-tutorial/ic769772.png "Associação a um Grupo")
   
    >[!NOTE]
    >Se não houver nenhum grupo listado, basta clicar em **AVANÇAR**. 
    > 

1. Selecione as pastas que você gostaria de colocar sob o controle do Syncplicity no computador do usuário e clique em **AVANÇAR**.
   
    ![Pastas do Syncplicity](./media/syncplicity-tutorial/ic769773.png "Pastas do Syncplicity")

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Syncplicity ou as APIs fornecidas pelo Syncplicity para provisionar as contas de usuário do AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Syncplicity.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Syncplicity, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Syncplicity**.

    ![Configurar o logon único](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Syncplicity no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Syncplicity.
## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

