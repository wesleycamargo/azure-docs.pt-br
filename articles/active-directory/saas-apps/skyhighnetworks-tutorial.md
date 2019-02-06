---
title: 'Tutorial: Integração do Azure Active Directory ao Skyhigh Networks | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Skyhigh Networks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: 6b66a7343c6ddaa9ff8cf36489798a96fbdf1c30
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175308"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Tutorial: Integração do Azure Active Directory ao Skyhigh Networks

Neste tutorial, você aprenderá a integrar a Skyhigh Networks ao Azure AD (Azure Active Directory).

A integração das redes Skyhigh com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso à Skyhigh Networks.
- Você pode permitir que seus usuários façam logon automaticamente no Skyhigh Networks (Single Sign-On) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Skyhigh Networks, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Skyhigh Networks

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Skyhigh Networks da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Adicionando o Skyhigh Networks da galeria
Para configurar a integração do Skyhigh Networks ao Azure AD, é necessário adicionar o Skyhigh Networks da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Skyhigh Networks da galeria, siga estas etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Skyhigh Networks**, selecione **Skyhigh Networks** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Skyhigh Networks na lista de resultados](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Skyhigh Networks, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte da Skyhigh Networks é para um usuário no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Skyhigh Networks.

Para configurar e testar o logon único do Azure AD com o Skyhigh Networks, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Skyhigh Networks](#create-a-skyhigh-networks-test-user)** – para ter um equivalente de Brenda Fernandes no Skyhigh Networks vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo do Skyhigh Networks.

**Para configurar o logon único do Azure AD com o Skyhigh Networks, siga estas etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Skyhigh Networks**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

1. Na seção **Domínio e URLs do Skyhigh Networks**, siga estas etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de URLs e de Domínio do Skyhigh Networks](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

     a. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de URLs e de Domínio do Skyhigh Networks](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte do cliente do Skyhigh Networks](mailto:support@skyhighnetworks.com) para obter esses valores. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do Skyhigh Networks**, clique em **Configurar Skyhigh Networks** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Skyhigh Networks](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

1. Para configurar o logon único no **lado da Skyhigh Networks**, você precisa enviar o **Certificado (Base64), o URL de saída, o ID de entidade SAML e o URL do Serviço de Logon Único SAML transferidos por download** para [ equipe de suporte da Skyhigh Networks](mailto:support@skyhighnetworks.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Criar um usuário de teste do Skyhigh Networks

Nesta seção, você criará um usuário chamado Brenda Fernandes no Skyhigh Networks. Trabalhe com a  [equipe de suporte do Skyhigh Networks](mailto:support@skyhighnetworks.com) para adicionar os usuários à plataforma Skyhigh Networks. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Skyhigh Networks.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Skyhigh Networks, siga estas etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Skyhigh Networks**.

    ![O link do Skyhigh Networks na lista de Aplicativos](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Skyhigh Networks no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Skyhigh Networks.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png

