---
title: 'Tutorial: Integração do Azure Active Directory ao Proxyclick | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: d93c5486d9c23558995742fc27e1222834cf4452
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446306"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Azure Active Directory com o Proxyclick

Neste tutorial, você aprenderá a integrar o Proxyclick ao Azure AD (Azure Active Directory).

A integração do Proxyclick ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Proxyclick.
- Você pode permitir que os usuários façam logon automaticamente no Proxyclick (Logon Único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Proxyclick, os seguintes itens são necessários:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Proxyclick

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Proxyclick por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-proxyclick-from-the-gallery"></a>Adição do Proxyclick por meio da galeria
Para configurar a integração do Proxyclick ao Azure AD, é preciso adicioná-lo por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Proxyclick por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Proxyclick**, selecione **Proxyclick** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Proxyclick na lista de resultados](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você fará a configuração e o teste do logon único do Azure AD com o Proxyclick, com base em uma usuária de testes chamada “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa conhecer o usuário equivalente do Proxyclick no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o respectivo usuário no Proxyclick.

Para fazer a configuração e o teste do logon único do Azure AD com o Proxyclick, conclua os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de testes do Proxyclick](#create-a-proxyclick-test-user)**: para ter um equivalente de Brenda Fernandes no Proxyclick que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Proxyclick.

**Para configurar o logon único do Azure AD com o Proxyclick, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Proxyclick**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Na seção **Domínio e URLs do Proxyclick**, execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado **IDP**:

    ![Informações de logon único em Domínio e URLs do Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://saml.proxyclick.com/init/<companyId>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://saml.proxyclick.com/consume/<companyId>`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Esses valores não são reais. Você atualizará esses valores com o identificador, URL de resposta e URL de logon reais, explicado mais adiante no tutorial.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/proxyclick-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Proxyclick**, clique em **Configurar o Proxyclick** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. Em outra janela do navegador da Web, faça logon no site de empresa do Proxyclick como administrador.

1. Selecione **Conta e configurações**.

    ![Configuração do Proxyclick](./media/proxyclick-tutorial/configure1.png)

1. Role para baixo até **INTEGRAÇÕES** e selecione **SAML**.

    ![Configuração do Proxyclick](./media/proxyclick-tutorial/configure2.png)

1. Na seção **SAML**, execute as seguintes etapas:

    ![Configuração do Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Copie o valor da **URL do consumidor SAML** e cole na caixa de texto **URL de resposta** na seção **Domínio e URLs do Proxyclick** do Portal do Azure.

    b. Copie o valor da **URL de redirecionamento SSO SAML** e cole nas caixas de texto **URL de logon** e **Identificador** na seção **Domínio e URLs do Proxyclick** do Portal do Azure.

    c. Selecione o **Método de solicitação SAML** como **Redirecionamento de HTTP**.

    d. Na caixa de texto **Emissor**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure.

    e. Na caixa de texto **URL do Ponto de Extremidade SAML 2.0**, cole o valor da **URL de Serviço de Logon Único do SAML** copiada do Portal do Azure.

    f. Abra o arquivo de certificado baixado do Portal do Azure no Bloco de Notas e cole-o na caixa de texto **Certificado**.

    g. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/proxyclick-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-proxyclick-test-user"></a>Criar um usuário de testes do Proxyclick

Para permitir que os usuários do Azure AD façam logon no Proxyclick, eles devem ser provisionados no Proxyclick. No caso do Proxyclick, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site de empresa do Proxyclick como administrador.

1. Clique em **Colegas** na barra de navegação superior.

    ![Adicionar Funcionário](./media/proxyclick-tutorial/user1.png)

1. Clique em **Adicionar Colega**

    ![Adicionar Funcionário](./media/proxyclick-tutorial/user2.png)

1. Na seção **Adicionar um colega**, execute as seguintes etapas:

    ![Adicionar Funcionário](./media/proxyclick-tutorial/user3.png)

    a. Na caixa de texto **Email**, digite o endereço de email do usuário, como **brittasimon@contoso.com**.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    d. Clique em **Adicionar Usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita Brenda Fernandes para usar o logon único do Azure concedendo acesso ao Proxyclick.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Proxyclick, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Proxyclick**.

    ![O link Proxyclick na lista de aplicativos](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Proxyclick no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Proxyclick.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

