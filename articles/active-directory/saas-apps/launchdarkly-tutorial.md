---
title: 'Tutorial: Integração do Azure Active Directory com o LaunchDarkly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420752"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integração do Azure Active Directory com o LaunchDarkly

Neste tutorial, você aprenderá a integrar o LaunchDarkly ao Azure AD (Azure Active Directory).

A integração do LaunchDarkly ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao LaunchDarkly.
- Você pode permitir que seus usuários façam logon automaticamente em LaunchDarkly (logon único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LaunchDarkly, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do LaunchDarkly

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar LaunchDarkly da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-launchdarkly-from-the-gallery"></a>Adicionar LaunchDarkly da Galeria
Para configurar a integração do LaunchDarkly ao Azure AD, você precisa adicionar o LaunchDarkly da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LauchDarkly da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **LaunchDarkly**, selecione **LaunchDarkly** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![LaunchDarkly na lista de resultados](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o LaunchDarkly, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LaunchDarkly é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LaunchDarkly.

Para configurar e testar o logon único do AD do Azure com o LaunchDarkly, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do LaunchDarkly](#create-a-launchdarkly-test-user)** – para ter um equivalente de Brenda Fernandes no LaunchDarkly vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo LaunchDarkly.

**Para configurar o logon único do Azure AD com o LaunchDarkly, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **LaunchDarkly**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. Na seção **Domínio e URLs do LaunchDarkly**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. Na caixa de texto **Identificador (ID da entidade)**, digite a URL: `app.launchdarkly.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > O valor de URL de Resposta não é real. Você atualizará o valor com a URL de Resposta real, que é explicada no tutorial posteriormente.

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.launchdarkly.com`

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do LaunchDarkly**, clique em **Configurar LaunchDarkly** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único** da **seção de Referência Rápida.**

    ![Configuração do LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do LaunchDarkly como administrador.

1. Selecione **Configurações de conta** do painel de navegação esquerdo.

    ![Configuração do LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

1. Clique na guia **Segurança**.

    ![Configuração do LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

1. Clique em **Habilitar SSO** e **Editar configuração de SAML**.

    ![Configuração do LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

1. Na seção **Editar sua configuração do SAML**, realize as seguintes etapas:

    ![Configuração do LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Copie a **URL de serviço do consumidor do SAML** para a sua instância e cole-a na caixa de texto URL de Resposta na seção **Domínio e URLs do LaunchDarkly** no portal do Azure.

    b. Na caixa de texto **URL de Logon Único**, cole o valor da **URL do Serviço de Logon Único** copiado do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa **certificado x. 509** ou você pode carregar diretamente o certificado clicando em **Carregar um**.

    d. Clique em **Salvar**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-launchdarkly-test-user"></a>Criar um usuário de teste LaunchDarkly

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no LaunchDarkly. O LaunchDarkly dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o LaunchDarkly, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao LaunchDarkly.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao LauchDarkly, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **LaunchDarkly**.

    ![O link do LaunchDarkly na lista de Aplicativos](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco LaunchDarkly no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo LaunchDarkly.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

