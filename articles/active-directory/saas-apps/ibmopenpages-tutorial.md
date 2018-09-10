---
title: 'Tutorial: Integração do Azure Active Directory com o IBM OpenPages | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IBM OpenPages.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 74d5dea5-2c48-464a-a7d0-cdd481c429d7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: dcc08596378f9e48bbeb379a5e939a77820bdc83
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436328"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-openpages"></a>Tutorial: Integração do Azure Active Directory com IBM OpenPages

Neste tutorial, você aprenderá como integrar o IBM OpenPages com o Azure Active Directory (Azure AD).

A integração do IBM OpenPages com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD que tem acesso ao IBM OpenPages.
- Você pode permitir que os usuários entrem automaticamente no IBM OpenPages (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o IBM OpenPages, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do IBM OpenPages

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o IBM OpenPages da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-ibm-openpages-from-the-gallery"></a>Adicionando o IBM OpenPages da galeria
Para configurar a integração do IBM OpenPages ao Azure AD, você precisa adicionar o IBM OpenPages da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o IBM OpenPages da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, insira **IBM OpenPages**, selecione **IBM OpenPages** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![IBM OpenPages na lista de resultados](./media/ibmopenpages-tutorial/tutorial_ibmopenpage_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, configure e teste o logon único do Azure AD com o IBM OpenPages baseado em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual é o usuário correspondente no IBM OpenPages a um usuário no Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no IBM OpenPages precisa ser estabelecida.

No IBM OpenPages, atribua o valor do **nome de usuário** no Azure AD como o valor de **Nome de usuário** para estabelecer a relação de link.

Para configurar e testar o logon único do Azure AD com o IBM OpenPages, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do IBM OpenPages](#create-an-ibm-openpages-test-user)**  – para ter um equivalente de Brenda Fernandes no IBM OpenPages que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo IBM OpenPages.

**Para configurar o logon único do Azure AD com o IBM OpenPages, execute as seguintes etapas:**

1. No portal do Azure, na página integração do aplicativo **IBM OpenPages**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/ibmopenpages-tutorial/tutorial_ibmopenpage_samlbase.png)

1. Na seção **Domínio e URLs do IBM OpenPages**, execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do IBM OpenPages](./media/ibmopenpages-tutorial/tutorial_ibmopenpage_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://<subdomain>.ibm.com:<ID>/openpages`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.ibm.com:<ID>/samlsps/op`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do IBM OpenPages](https://www.ibm.com/support/home/) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/ibmopenpages-tutorial/tutorial_ibmopenpage_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/ibmopenpages-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no **IBM OpenPages**, você precisa enviar os **XML de Metadados** baixados para a [equipe de suporte do IBM OpenPages](https://www.ibm.com/support/home/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/ibmopenpages-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/ibmopenpages-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/ibmopenpages-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/ibmopenpages-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-ibm-openpages-test-user"></a>Criar um usuário de teste do IBM OpenPages

Nesta seção, você vai criar um usuário chamado Brenda Fernandes no IBM OpenPages. Trabalhe com a [equipe de suporte do IBM OpenPages](https://www.ibm.com/support/home/) para adicionar os usuários na plataforma IBM OpenPages. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao IBM OpenPages.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao IBM OpenPages, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **IBM OpenPages**.

    ![O link do IBM OpenPages na lista de aplicativos](./media/ibmopenpages-tutorial/tutorial_ibmopenpage_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco IBM OpenPages no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo IBM OpenPages.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ibmopenpages-tutorial/tutorial_general_01.png
[2]: ./media/ibmopenpages-tutorial/tutorial_general_02.png
[3]: ./media/ibmopenpages-tutorial/tutorial_general_03.png
[4]: ./media/ibmopenpages-tutorial/tutorial_general_04.png

[100]: ./media/ibmopenpages-tutorial/tutorial_general_100.png

[200]: ./media/ibmopenpages-tutorial/tutorial_general_200.png
[201]: ./media/ibmopenpages-tutorial/tutorial_general_201.png
[202]: ./media/ibmopenpages-tutorial/tutorial_general_202.png
[203]: ./media/ibmopenpages-tutorial/tutorial_general_203.png

