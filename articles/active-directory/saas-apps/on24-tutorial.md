---
title: 'Tutorial: Integração do Azure Active Directory ao ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070850"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integração do Azure Active Directory ao ON24 Virtual Environment SAML Connection

Neste tutorial, você aprenderá a integrar o ON24 Virtual Environment SAML Connection ao Azure AD (Azure Active Directory).

A integração do ON24 Virtual Environment SAML Connection com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ON24 Virtual Environment SAML Connection.
- Você pode permitir que os usuários façam logon automaticamente no ON24 Virtual Environment SAML Connection (logon único) com a conta do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ON24 Virtual Environment SAML Connection, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para o logon único no ON24 Virtual Environment SAML Connection

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ON24 Virtual Environment SAML Connection da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adicionando o ON24 Virtual Environment SAML Connection da galeria
Para configurar a integração do ON24 Virtual Environment SAML Connection ao Azure AD, você precisa adicionar o ON24 Virtual Environment SAML Connection da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ON24 Virtual Environment SAML Connection da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/on24-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **ON24 Virtual Environment SAML Connection**, selecione **ON24 Virtual Environment SAML Connection** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ON24 Virtual Environment SAML Connection com base em um usuário de teste chamado "Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber que o usuário no ON24 Virtual Environment SAML Connection é equivalente a um usuário no Azure AD. Em outras palavras, precisa ser estabelecida uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ON24 Virtual Environment SAML Connection.

Para configurar e testar o logon único do Azure AD com o ON24 Virtual Environment SAML Connection, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ON24 Virtual Environment SAML Connection](#create-an-on24-virtual-environment-saml-connection-test-user)** – para ter um equivalente de Brenda Fernandes no ON24 Virtual Environment SAML Connection que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura-o no aplicativo ON24 Virtual Environment SAML Connection.

**Para configurar o logon único do Azure AD com o ON24 Virtual Environment SAML Connection, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **ON24 Virtual Environment SAML Connection**, selecione **logon único**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML** para habilitar o logon único.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL:

     **URL do ambiente de produção**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL do ambiente de garantia de qualidade**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. Na caixa de texto **URL de resposta**, insira uma URL:
    
     **URL do ambiente de produção**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL do ambiente de garantia de qualidade**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Clique em **Definir URLs adicionais**. 

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Se você desejar configurar o aplicativo no modo iniciado por **SP**, na caixa de texto **URL de logon**, digite uma URL: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Baixar** para baixar o certificado apropriado de acordo com seus requisitos e salve-o no computador.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Para configurar o logon único no lado do **ON24 Virtual Environment SAML Connection**, é necessário enviar o certificado ou os metadados que você baixou do portal do Azure para a [equipe de suporte do ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/on24-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute etapas a seguir.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Criar um usuário de teste do ON24 Virtual Environment SAML Connection

Nesta seção, você criará um usuário chamado Brenda Fernandes no ON24 Virtual Environment SAML Connection. Trabalhe com a [equipe de suporte do ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) para adicionar os usuários na plataforma ON24 Virtual Environment SAML Connection. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure permitindo seu acesso ao ON24 Virtual Environment SAML Connection.

1. No portal do Azure, selecione **Aplicativos Empresariais** e **Todos os aplicativos**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **ON24 Virtual Environment SAML Connection**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Clique no botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ON24 Virtual Environment SAML Connection no painel de acesso, você entra automaticamente no aplicativo ON24 Virtual Environment SAML Connection.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

