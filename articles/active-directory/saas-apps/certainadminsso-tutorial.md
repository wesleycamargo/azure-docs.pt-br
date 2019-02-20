---
title: 'Tutorial: Integração do Azure Active Directory com o Certain Admin SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Certain Admin SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 98ba0174-be02-408a-8634-c8113b12dedb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d85e8dbac47bd41c759e9c225df5544c659cc05
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205521"
---
# <a name="tutorial-azure-active-directory-integration-with-certain-admin-sso"></a>Tutorial: Integração do Azure Active Directory com o Certain Admin SSO

Neste tutorial, você aprende a integrar o Certain Admin SSO ao Azure AD (Azure Active Directory).

A integração do Certain Admin SSO ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Certain Admin SSO.
- Você pode permitir que seus usuários entrem automaticamente no Certain Admin SSO por meio do logon único (SSO) usando suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Certain Admin SSO, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Certain Admin SSO

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Certain Admin SSO da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-certain-admin-sso-from-the-gallery"></a>Adicionar Certain Admin SSO da galeria
Para configurar a integração do Certain Admin SSO ao Azure AD, é necessário adicionar o Certain Admin SSO à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Certain Admin SSO por meio da galeria, execute as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Certain Admin SSO**, selecione **Certain Admin SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Certain Admin SSO na lista de resultados](./media/certainadminsso-tutorial/tutorial_certainadminsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Certain Admin SSO, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Certain Admin SSO é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Certain Admin SSO.

Para configurar e testar o logon único do Azure AD com o Certain Admin SSO, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Certain Admin SSO](#create-a-certain-admin-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Certain Admin SSO que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo Certain Admin SSO.

**Para configurar o logon único do Azure AD com o Certain Admin SSO, execute as etapas abaixo:**

1. No portal do Azure, na página de integração do aplicativo **Certain Admin SSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/certainadminsso-tutorial/tutorial_certainadminsso_samlbase.png)

1. Na seção **URLs e Domínio do Certain Admin SSO**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Certain Admin SSO](./media/certainadminsso-tutorial/tutorial_certainadminsso_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<YOUR DOMAIN URL>/svcs/sso_admin_login/handleRequest/<ID>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.certain.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Certain Admin SSO](mailto:integrations@certain.com) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/certainadminsso-tutorial/tutorial_certainadminsso_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/certainadminsso-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Certain Admin SSO**, clique em **Configurar Certain Admin SSO** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Certain Admin SSO](./media/certainadminsso-tutorial/tutorial_certainadminsso_configure.png) 

1. Para configurar o logon único no lado do **Certain Admin SSO**, é necessário enviar o **Certificado (Bruto)** baixado, a **URL de Saída, ID da Entidade SAML e URL do Serviço de Logon Único** para a [equipe de suporte do Certain Admin SSO](mailto:integrations@certain.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/certainadminsso-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/certainadminsso-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/certainadminsso-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/certainadminsso-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-certain-admin-sso-test-user"></a>Criar um usuário de teste do Certain Admin SSO

Nesta seção, você cria um usuário chamado Brenda Fernandes no Certain Admin SSO. Trabalhe com a  [equipe de suporte do Certain Admin SSO](mailto:integrations@certain.com) para adicionar usuários na plataforma do Certain Admin SSO. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Certain Admin SSO.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Certain Admin SSO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Certain Admin SSO**.

    ![O link do Certain Admin SSO na lista de Aplicativos](./media/certainadminsso-tutorial/tutorial_certainadminsso_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Certain Admin SSO no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Certain Admin SSO.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/certainadminsso-tutorial/tutorial_general_01.png
[2]: ./media/certainadminsso-tutorial/tutorial_general_02.png
[3]: ./media/certainadminsso-tutorial/tutorial_general_03.png
[4]: ./media/certainadminsso-tutorial/tutorial_general_04.png

[100]: ./media/certainadminsso-tutorial/tutorial_general_100.png

[200]: ./media/certainadminsso-tutorial/tutorial_general_200.png
[201]: ./media/certainadminsso-tutorial/tutorial_general_201.png
[202]: ./media/certainadminsso-tutorial/tutorial_general_202.png
[203]: ./media/certainadminsso-tutorial/tutorial_general_203.png

