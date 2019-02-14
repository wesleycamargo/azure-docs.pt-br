---
title: 'Tutorial: Integração do Azure Active Directory ao EverBridge | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95465fdc17131c996fa242d028addbab4191628c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191105"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Azure Active Directory ao EverBridge

Neste tutorial, você aprenderá a integrar o EverBridge ao Azure AD (Azure Active Directory).

A integração do EverBridge ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao EverBridge.
- Você pode permitir que seus usuários façam logon automaticamente no EverBridge (Single Sign-On) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao EverBridge, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do EverBridge

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar EverBridge da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-everbridge-from-the-gallery"></a>Adicionar EverBridge da galeria

Para configurar a integração do EverBridge ao Azure AD, você precisará adicionar o EverBridge da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o EverBridge da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **EverBridge**, selecione **EverBridge** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![EverBridge na lista de resultados](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o EverBridge com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do EverBridge é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EverBridge.

Para configurar e testar o logon único do Azure AD com o EverBridge, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do EverBridge](#creating-an-everbridge-test-user)** – para ter um equivalente de Brenda Fernandes no EverBridge que esteja vinculado à representação desse usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo EverBridge.

**Para configurar o logon único do Azure AD com o EverBridge, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **EverBridge**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

    >[!NOTE]
    >Você precisa fazer as configurações do aplicativo OU como o Portal do Gerente OU como o Portal do Membro em ambas as extremidades, ou seja, no Portal do Azure e no Portal Everbridge.

4. Para configurar o aplicativo **EverBridge** como o **Portal do EverBridge Manager**, na seção **Configuração Básica do SAML** execute as seguintes etapas:

    ![Informações de logon único do domínio e URLs do EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://sso.everbridge.net/<API_Name>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do EverBridge](mailto:support@everbridge.com) para obter esses valores.

5. Para configurar o aplicativo **EverBridge** como o **Portal de Membros do EverBridge**, na seção **Configuração Básica do SAML**, execute as seguintes etapas:

    * Se você deseja configurar o aplicativo no **modo iniciado pelo IDP**:

        ![Informações de logon único do domínio e URLs do EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

        ![Informações de logon único do domínio e URLs do EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do EverBridge](mailto:support@everbridge.com) para obter esses valores.

6. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Na seção **Configurar o EverBridge**, copie o URL apropriado de acordo com sua exigência.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do EverBridge](common/configuresection.png)

8. Para obter o SSO configurado para o **EverBridge** como o aplicativo do **EverBridge Manager Portal**, execute as seguintes etapas: 
 
9. Em uma janela do navegador da Web diferente, faça o login no EverBridge como administrador.

9. No menu na parte superior, clique na guia **Configurações** e selecione **Logon Único** em **Segurança**.
   
    ![Configurar o logon único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na caixa de texto **Nome**, digite o nome do Provedor do Identificador (por exemplo: o nome de sua empresa).
   
    b. Na caixa de texto **Nome da API** , digite o nome da API.
   
    c. Clique no botão **Escolher Arquivo** para carregar o arquivo de metadados que você baixou do portal do Azure.
   
    d. No Local de Identidade do SAML, selecione **A identidade está no elemento NameIdentifier da instrução Subject**.
   
    e. Na caixa de texto **URL de login do Provedor de identidade**, cole o valor de **URL de login** que você copiou do portal do Azure.
   
    f. Na Associação de Solicitação Iniciada pelo Provedor de Serviço, selecione **Redirecionamento HTTP**.

    g. Clique em **Salvar**

10. Para configurar o single sign-on no **aplicativo EverBridge** como **EverBridge Member Portal**, você precisa enviar o **XML Metadata de Federação** para [equipe de suporte Everbridge](mailto:support@everbridge.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
  
### <a name="creating-an-everbridge-test-user"></a>Criar um usuário de teste do EverBridge

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Everbridge. Trabalhe com a [equipe de suporte do EverBridge](mailto:support@everbridge.com) para adicionar os usuários na plataforma do Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao EverBridge.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **EverBridge**.

    ![Configurar o logon único](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica na peça EverBridge no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo EverBridge.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
