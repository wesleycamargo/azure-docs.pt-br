---
title: 'Tutorial: Integração do Azure Active Directory com o Adaptive Insights | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884873"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Tutorial: Integração do Azure Active Directory com o Adaptive Insights

Neste tutorial, você aprenderá a integrar o Adaptive Insights ao Azure AD (Azure Active Directory).

A integração do Adaptive Insights ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Adaptive Insights.
- É possível habilitar seus usuários para fazer logon automaticamente no Adaptive Insights (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Adaptive Insights, são necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Adaptive Insights

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adaptive Insights da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionando o Adaptive Insights da galeria
Para configurar a integração do Adaptive Insights com o Azure AD, você precisa adicionar o Adaptive Insights por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adaptive Insights da galeria, siga estas etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Adaptive Insights**, selecione **Adaptive Insights** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adaptive Insights, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Adaptive Insights é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Adaptive Insights.

Para configurar e testar o logon único do Azure AD com o Adaptive Insights, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Adaptive Insights](#create-an-adaptive-insights-test-user)**: para ter um equivalente de Brenda Fernandes no Adaptive Insights que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Adaptive Insights.

**Para configurar o logon único do Azure AD com o Adaptive Insights, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Adaptive Insights**, selecione **Logon único**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Selecionar um método de conexão única**, selecione o modo **SAML** para ativar o logon único.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir, se desejar configurar o aplicativo no modo **IDP**:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

     a. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > É possível obter valores de URL de resposta e Identificador (ID da entidade) na página **Configurações de SSO do SAML** do Adaptive Insights.
 
5. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do certificado **(Base64)** e salve no seu computador.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Na seção **Configurar Adaptive Insights**, copie a URL apropriada de acordo com suas necessidades.

    Observe que a URL pode dizer o seguinte:

     a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa Adaptive Insights como administrador.

8. Vá para **Administrador**.

    ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

9. Na seção **Usuários e Funções**, clique em **Gerenciar Configurações de SSO do SAML**.

    ![Gerenciar Configurações de SSO do SAML](./media/adaptivesuite-tutorial/IC805645.png "Gerenciar Configurações de SSO do SAML")

10. Na página **Configurações de SSO do SAML** , realize as seguintes etapas:

    ![Configurações de SSO do SAML](./media/adaptivesuite-tutorial/IC805646.png "Configurações de SSO do SAML")

     a. Na caixa de texto **Nome do provedor de identidade** , digite um nome para a sua configuração.

    b. Cole o **Identificador do Azure AD** copiado do Portal do Azure na caixa de texto **ID da Entidade do Provedor de Identidade**.

    c. Cole o valor da **URL de Logon** copiado do Portal do Azure na caixa de texto **URL de SSO do Provedor de identidade**.

    d. Cole o valor da **URL de Logoff** copiado do Portal do Azure na caixa de texto **URL de Logoff personalizado**.

    e. Para carregar seu certificado baixado, clique em **Escolher arquivo**.

    f. Selecione as seguintes opções para:

    * **ID de usuário do SAML**, selecione **Nome de usuário do Adaptive Insights do Usuário**.

    * **Local da ID de usuário do SAML**, selecione **ID de usuário em NameID da Entidade**.

    * **Formato de NameID do SAML**, selecione **Endereço de email**.

    * **Habilitar SAML**, selecione **Permitir SSO do SAML e direcionar logon do Adaptive Insights**.

    g. Copie **URL de SSO do Adaptive Insights** e cole-a nas caixas de texto **Identificador(ID da entidade)** e **URL de resposta** na seção **URLs e Domínio do Adaptive Insights** no portal do Azure.

    h. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** tipo de campo **brendafernandes\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Criar um usuário de teste do Adaptive Insights

Para permitir que os usuários do Azure AD façam logon no Adaptive Insights, eles devem ser provisionados no Adaptive Insights. No caso do Adaptive Insights, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:** 

1. Faça logon em seu site de empresa **Adaptive Insights** como administrador.
2. Vá para **Administrador**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Na seção **Usuários e Funções**, clique em **Adicionar Usuário**.

   ![Adicionar Usuário](./media/adaptivesuite-tutorial/IC805648.png "Adicionar Usuário")
   
4. Na seção **Novo Usuário** , realize as seguintes etapas:

   ![Enviar](./media/adaptivesuite-tutorial/IC805649.png "Enviar")

    a. Digite o **Nome**, **Logon**, **Email** e **Senha** de um usuário válido do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

   b. Selecione uma **Função**.

   c. Clique em **Enviar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação de conta de usuário do Adaptive Insights ou as APIs fornecidas pelo Adaptive Insights para provisionar as contas de usuário do AAD.
>

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Adaptive Insights.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Adaptive Insights**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clica no bloco Adaptive Insights no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Adaptive Insights.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
