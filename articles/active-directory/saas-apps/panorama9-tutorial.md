---
title: 'Tutorial: Integração do Azure Active Directory com o Panorama9 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 77e370e80e423446d17a074b9458e7002f8627dd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448339"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Active Directory do Azure com o Panorama9

Neste tutorial, você aprende a integrar o Panorama9 ao Azure AD (Azure Active Directory).

A integração do Panorama9 ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Panorama9
- É possível permitir que os usuários se conectem automaticamente ao Panorama9 (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Panorama9, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Panorama9

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Panorama9 por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-panorama9-from-the-gallery"></a>Adicionando o Panorama9 por meio da galeria
Para configurar a integração do Panorama9 ao Azure AD, é necessário adicionar o Panorama9 à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Panorama9 por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Panorama9**.

    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/tutorial_panorama9_search.png)

1. No painel de resultados, selecione **Panorama9** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configura e testa o logon único do Azure AD com o Panorama9, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Panorama9 é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Panorama9.

No Panorama9, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Panorama9, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Panorama9](#creating-a-panorama9-test-user)** – para ter um equivalente de Brenda Fernandes no Panorama9 que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Panorama9.

**Para configurar o logon único do Azure AD com o Panorama9, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Panorama9**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

1. Na seção **Domínio e URLs do Panorama9**, realize as seguintes etapas:

    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL como: `https://dashboard.panorama9.com/saml/access/3262`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Panorama9](https://support.panorama9.com) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Panorama9**, clique em **Configurar o Panorama9** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Panorama9 como administrador.

1. Na barra de ferramentas na parte superior, clique em **Gerenciar** e em **Extensões**.
   
   ![Extensões](./media/panorama9-tutorial/ic790023.png "Extensões")
1. No diálogo **Extensões**, clique em **Logon Único**.
   
   ![Logon Único](./media/panorama9-tutorial/ic790024.png "Logon Único")
1. Na seção **Configurações** , realize as seguintes etapas:
   
   ![Configurações](./media/panorama9-tutorial/ic790025.png "Configurações")
   
    a. Na caixa de texto **URL do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único** copiado do portal do Azure.
   
    b. Na caixa de texto **Impressão digital do certificado**, cole o valor da **Impressão Digital** do certificado copiado do portal do Azure.    
         
1. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-panorama9-test-user"></a>Criando um usuário de teste do Panorama9

Para permitir que os usuários do AD do Azure façam logon no Panorama9, eles devem ser provisionados no Panorama9.  

No caso do Panorama9, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Panorama9** como administrador.

1. No menu na parte superior, clique em **Gerenciar** e em **Usuários**.
   
  ![Usuários](./media/panorama9-tutorial/ic790027.png "Usuários")

1. Na seção Usuários, clique em **+** para adicionar um novo usuário.

 ![Usuários](./media/panorama9-tutorial/ic790028.png "Usuários")

1. Acesse a seção Dados do usuário e digite o endereço de email de um usuário válido do Azure Active Directory que você deseja provisionar na caixa de texto **Email**.

1. Acesse a seção Usuários e clique em **Salvar**.
   
> [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Panorama9.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Panorama9, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Panorama9**.

    ![Configurar o logon único](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Panorama9 no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Panorama9.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

