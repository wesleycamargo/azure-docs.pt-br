---
title: 'Tutorial: Integração do Active Directory do Azure com o Phraseanet | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Azure Active Directory e o Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: cfeadbf1d46d9e36f8619cafe29d9dd69aad6eec
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118038"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Tutorial: Integração do Active Directory do Azure com o Phraseanet

Neste tutorial, você aprenderá como integrar o Phraseanet ao Azure AD (Azure Active Directory).

A integração do Phraseanet ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Phraseanet.
- Você pode permitir que seus usuários entrem automaticamente no Phraseanet (Logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Phraseanet, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para um Phraseanet o logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Phraseanet da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-phraseanet-from-the-gallery"></a>Adicionando Phraseanet da galeria
Para configurar a integração do Phraseanet ao Azure AD, você precisa adicionar o Phraseanet da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Phraseanet da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Phraseanet**, selecione **Phraseanet** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Phraseanet com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual é o usuário de contraparte no Phraseanet para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Phraseanet precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Phraseanet, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste Phraseanet](#create-a-phraseanet-test-user)** – para ter um equivalente de Britta Simon no Phraseanet que esteja vinculado à representação de usuário do AD do Azure.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Phraseanet.

**Para configurar o logon único do Azure AD com o Phraseanet, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), no **Phraseanet** página de integração de aplicativos, selecione **sign-on único**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. Sobre o **selecionar um método de logon único** caixa de diálogo, clique em **selecionar** para **SAML** modo para habilitar o logon único.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. Sobre o **definir o logon único com SAML** , clique em **editar** botão para abrir **básicas de configuração de SAML** caixa de diálogo.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. Sobre o **básicas de configuração de SAML** , realize as seguintes etapas:

    No **URL de logon** caixa de texto, digite uma URL como:  `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a equipe de suporte do [Phraseanet](mailto:support@alchemy.fr) para obter o valor.
 
5. No **definir o logon único com SAML** página, o **certificado de autenticação SAML** seção, clique em **baixar** para baixar o **XML de metadados de Federação**  e salve-o em seu computador.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Para configurar o logon único no **Phraseanet** , é necessário enviar o **XML de metadados de Federação** para [equipe de suporte do Phraseanet](mailto:support@alchemy.fr). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Active Directory do Azure**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. Nas propriedades do usuário, execute as seguintes etapas.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. No **nome** Inserir campo **Brendafernandes**.
  
    b. No **nome de usuário** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-phraseanet-test-user"></a>Criar um usuário de teste Phraseanet

Nesta seção, você cria um usuário chamado Brenda Fernandes no Phraseanet. Trabalhar com [equipe de suporte do Phraseanet](mailto:support@alchemy.fr) para adicionar os usuários na plataforma do Phraseanet. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Phraseanet.

1. No portal do Azure, selecione **Aplicativos Corporativos**, selecione **Todos os aplicativos**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Selecione o **Add** botão e, em seguida, selecione **usuários e grupos** no **Adicionar atribuição** caixa de diálogo.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. No **usuários e grupos** diálogo, selecione **Britta Simon** na lista de usuários, em seguida, clique o **selecione** botão na parte inferior da tela.

5. Na caixa de diálogo **Add Assignment**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Phraseanet no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Phraseanet.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


