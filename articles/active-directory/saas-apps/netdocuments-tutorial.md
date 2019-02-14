---
title: 'Tutorial: Integração do Azure Active Directory ao NetDocuments | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9af1198ed302eba9761c70fef621f15dfd67bc09
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185070"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Active Directory do Azure ao NetDocuments

Neste tutorial, você aprenderá a integrar o NetDocuments ao Azure AD (Azure Active Directory).

A integração do NetDocuments ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao NetDocuments
- Você pode permitir que seus usuários façam logon automaticamente no NetDocuments (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao NetDocuments, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do NetDocuments

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o NetDocuments da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar o NetDocuments da galeria
Para configurar a integração do NetDocuments ao Azure AD, você precisará adicionar o NetDocuments da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o NetDocuments da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **NetDocuments**.

    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

1. No painel de resultados, selecione **NetDocuments** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o NetDocuments, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do NetDocuments é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no NetDocuments.

No NetDocuments, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o NetDocuments, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário de teste do NetDocuments](#creating-a-netdocuments-test-user)** – para ter um equivalente de Brenda Fernandes no NetDocuments que esteja vinculado à representação desse usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo NetDocuments.

**Para configurar o logon único do Azure AD com o NetDocuments, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **NetDocuments**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

1. Na seção **URLs e Domínio do NetDocuments**, execute as seguintes etapas:

    ![Configurar o logon único](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do NetDocuments](https://support.netdocuments.com/hc/) para obter esses valores.
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/netdocuments-tutorial/tutorial_general_400.png)

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do NetDocuments como administrador.

1. Vá para **Administrador**.

1. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

1. Clique em **Configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/ic795048.png "Configurar opções de autenticação avançadas")

1. Na caixa de diálogo **Identidade Federada**, execute as seguintes etapas:
   
    ![Identidade Federada](./media/netdocuments-tutorial/ic795049.png "Identidade Federada")
   
     a. Para **Tipo de servidor de identidade federada**, selecione **Serviços de Federação do Active Directory**.
   
    b. Clique em **Escolher arquivo**para carregar o arquivo de metadados que você baixou do Portal do Azure.
   
    c. Clique em **OK**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-netdocuments-test-user"></a>Criar um usuário de teste do NetDocuments

Para permitir que os usuários do Azure AD façam logon no NetDocuments, eles devem ser provisionados no NetDocuments.  
No caso do NetDocuments, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **NetDocuments** como administrador.

1. No menu na parte superior, clique em **Administrador**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

1. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

1. Na caixa de texto **Endereço de Email**, digite o endereço de email de uma conta válida do Azure Active Directory que você deseja provisionar e clique em **Adicionar Usuário**.
   
    ![Endereço de Email](./media/netdocuments-tutorial/ic795053.png "Endereço de Email")
   
   >[!NOTE]
   >O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa. É possível usar qualquer outra ferramenta de criação da conta de usuário do NetDocuments ou as APIs fornecidas pelo NetDocuments para provisionar as contas de usuário do Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você concederá a Brenda Fernandes acesso ao NetDocuments para que ela fique habilitada a usar o logon único do Azure.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao NetDocuments, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **NetDocuments**.

    ![Configurar o logon único](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco NetDocuments no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo NetDocuments.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

