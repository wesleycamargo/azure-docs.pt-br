---
title: 'Tutorial: Integração do Azure Active Directory ao TonicDM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TonicDM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 19ea9a07-9ecf-43dc-91ba-593ce3c00b01
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: b582af22707b7bff187ed93fb48ba96d15634ab3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433317"
---
# <a name="tutorial-azure-active-directory-integration-with-tonicdm"></a>Tutorial: Integração do Azure Active Directory ao TonicDM

Neste tutorial, você aprende a integrar o TonicDM ao Azure AD (Azure Active Directory).

A integração do TonicDM ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao TonicDM.
- Você pode permitir que usuários façam logon automaticamente no TonicDM (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TonicDM, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TonicDM

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o TonicDM da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-tonicdm-from-the-gallery"></a>Adicionando o TonicDM da galeria
Para configurar a integração do TonicDM ao Azure AD, você precisa adicionar o TonicDM da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TonicDM da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **TonicDM**, selecione **TonicDM** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![TonicDM na lista de resultados](./media/tonicdm-tutorial/tutorial_tonicdm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TonicDM, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TonicDM é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TonicDM.

Para configurar e testar o logon único do Azure AD com o TonicDM, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do TonicDM](#create-a-tonicdm-test-user)** – para ter um equivalente de Britta Simon no TonicDM vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo TonicDM.

**Para configurar o logon único do Azure AD com o TonicDM, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **TonicDM**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/tonicdm-tutorial/tutorial_tonicdm_samlbase.png)

1. Na seção **Domínio e URLs do TonicDM**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do TonicDM](./media/tonicdm-tutorial/tutorial_tonicdm_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL: `https://tonicdm.com/`

    b. Na caixa de texto **Identificador**, digite uma URL: `https://tonicdm.com/saml/metadata`

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/tonicdm-tutorial/tutorial_tonicdm_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/tonicdm-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do TonicDM**, clique em **Configurar TonicDM** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do TonicDM](./media/tonicdm-tutorial/tutorial_tonicdm_configure.png) 

1. Para configurar o logon único no lado do **TonicDM**, é necessário enviar o **Certificado (Base64) baixado, a URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** para a [equipe de suporte do TonicDM](mailto:support@tonicdm.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/tonicdm-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/tonicdm-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/tonicdm-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/tonicdm-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-tonicdm-test-user"></a>Criar um usuário de teste do TonicDM

Nesta seção, você criará uma usuária chamada Britta Simon no TonicDM. Trabalhe com a [equipe de suporte do TonicDM](mailto:support@tonicdm.com) para adicionar os usuários na plataforma do TonicDM. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao TonicDM.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao TonicDM, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **TonicDM**.

    ![O link do TonicDM na lista Aplicativos](./media/tonicdm-tutorial/tutorial_tonicdm_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco TonicDM no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo TonicDM.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tonicdm-tutorial/tutorial_general_01.png
[2]: ./media/tonicdm-tutorial/tutorial_general_02.png
[3]: ./media/tonicdm-tutorial/tutorial_general_03.png
[4]: ./media/tonicdm-tutorial/tutorial_general_04.png

[100]: ./media/tonicdm-tutorial/tutorial_general_100.png

[200]: ./media/tonicdm-tutorial/tutorial_general_200.png
[201]: ./media/tonicdm-tutorial/tutorial_general_201.png
[202]: ./media/tonicdm-tutorial/tutorial_general_202.png
[203]: ./media/tonicdm-tutorial/tutorial_general_203.png

