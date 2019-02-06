---
title: 'Tutorial: Integração do Azure Active Directory ao Vidyard | Microsoft Docs'
description: Aprenda como configurar logon único entre o Azure Active Directory e o Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 683cdc43c8d671c14142d0770cd2594ad862b7ac
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196227"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Tutorial: Integração do Azure Active Directory ao Vidyard

Neste tutorial, você aprenderá como integrar o Vidyard com Azure AD (Azure Active Directory).

A integração do Vidyard com Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Vidyard.
- Você pode permitir que usuários façam logon automaticamente no Vidyard (logon único) com as respectivas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Vidyard, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Vidyard

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Vidyard da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-vidyard-from-the-gallery"></a>Adicionar Vidyard da galeria
Para configurar a integração do Vidyard ao Azure AD, você precisa adicionar o Vidyard da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Vidyard da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Vidyard**, selecione **Vidyard** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Vidyard na lista de resultados](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Vidyard, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Vidyard é equivalente a um usuário do Azure AD. Em outras palavras, um relacionamento de vínculo entre um usuário do Azure AD e o usuário relacionado no Vidyard precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com Vidyard, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Vidyard](#create-a-vidyard-test-user)** – para ter um equivalente de Brenda Fernandes no Vidyard vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Vidyard.

**Para configurar o logon único do Azure AD com o Vidyard, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Vidyard**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Na seção **Domínio e URLs do Vidyard**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Voyance](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Voyance](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Esses valores não são reais. Você atualizará esses valores com o Identificador real, a URL de Resposta e URL de Logon, que serão explicados posteriormente no tutorial

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/vidyard-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Vidyard**, clique em **Configurar Vidyard** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Vidyard Software como administrador.

1. No painel do Vidyard, selecione **Grupo** > **Segurança**

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure1.png)

1. Clique na guia **Novo Perfil**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure2.png)

1. Na seção **Configuração SAML**, execute as seguintes etapas:

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure3.png)

     a. Insira o nome do perfil geral na caixa de texto **Nome do Perfil**.

    b. Copie o valor da **Página de Logon do Usuário de SSO** e cole-o na caixa de texto **URL de Entrada** na seção **Domínio e URLs do Voyance** no portal do Azure.

    c. Copie o valor da **URL do ACS** e cole-o na caixa de texto **URL de Resposta** na seção **Domínio e URLs do Voyance** no portal do Azure.

    d. Copie o valor de **Emissor/URL de Metadados** e cole-o na caixa de texto **Identificador** na seção **Domínio e URLs do Voyance** no portal do Azure.

    e. Abra o arquivo de certificado baixado do portal do Azure no Bloco de Notas e cole-o na caixa de texto **Certificado X.509**.

    f. Na caixa de texto **URL do Ponto de Extremidade SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** copiada do Portal do Azure.

    g. Clique em **Confirmar**.

1. Na guia Logon Único, selecione **Atribuir** ao lado de um perfil existente

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Depois de criar um perfil de SSO, atribua-o a qualquer grupo para o qual os usuários precisarão de acesso através do Azure. Se o usuário não existir dentro do grupo ao qual ele foi atribuído, o Vidyard criará automaticamente uma conta de usuário e atribuirá a função em tempo real.

1. Selecione o grupo da organização, que fica visível nos **Grupos Disponíveis para Atribuir**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure5.png)

1. É possível visualizar os grupos atribuídos em **Grupos Atualmente Atribuídos**. Selecione uma função para o grupo de acordo com a organização e clique em **Confirmar**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Para mais informações, consulte [este documento](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/vidyard-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/vidyard-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/vidyard-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-vidyard-test-user"></a>Crie um usuário de teste do Vidyard

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Vidyard. O Vidyard dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Vidyard, caso ele ainda não exista.
>[!Note]
>Caso precise criar um usuário manualmente, contate a  [equipe de suporte do Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Vidyard.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Vidyard, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Vidyard**.

    ![O link do Vidyard na lista de Aplicativos](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Vidyard no Painel de Acesso, deverá entrar automaticamente no aplicativo do Vidyard.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

