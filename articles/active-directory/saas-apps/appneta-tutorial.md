---
title: 'Tutorial: integração do Azure Active Directory ao AppNeta Performance Monitor | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: ccedc0288e313df2639862a14078d8cad9951286
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054542"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Tutorial: integração do Azure Active Directory ao AppNeta Performance Monitor

Neste tutorial, você aprenderá a integrar o AppNeta Performance Monitor ao Azure AD (Azure Active Directory).

A integração do AppNeta Performance Monitor ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao AppNeta Performance Monitor.
- Você pode permitir que os usuários façam logon automaticamente no AppNeta Performance Monitor (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AppNeta Performance Monitor, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura de logon único do AppNeta Performance Monitor

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o AppNeta Performance Monitor a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Adicionar o AppNeta Performance Monitor a partir da galeria
Para configurar a integração do AppNeta Performance Monitor ao Azure AD, você precisará adicionar o AppNeta Performance Monitor da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o AppNeta Performance Monitor da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite**AppNeta Performance Monitor**, selecione **AppNeta Performance Monitor** do painel de resultado e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![AppNeta Performance Monitor na lista de resultados](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o AppNeta Performance Monitor, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AppNeta Performance Monitor é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AppNeta Performance Monitor.

Para configurar e testar o logon único do Azure AD com o AppNeta Performance Monitor, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do AppNeta Performance Monitor](#create-an-appneta-performance-monitor-test-user)** – para ter um equivalente de Brenda Fernandes no AppNeta Performance Monitor que está vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo portal AppNeta Performance Monitor.

**Para configurar o logon único do AD do Azure com o AppNeta Performance Monitor, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **portal AppNeta Performance Monitor**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. Na seção **Domínio e URLs do AppNeta Performance Monitor**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do AppNeta Performance Monitor](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.pm.appneta.com`

    b. Na caixa de texto **Identificador**, digite o valor: `PingConnect`

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte do AppNeta Performance Monitor](mailto:support@appneta.com) para obter esse valor. 

5. O aplicativo AppNeta Performance Monitor espera as declarações do SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados de acordo com a sua configuração. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo.

    ![Configurar o logon único](./media/appneta-tutorial/attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
           
    | Nome do atributo | Valor do atributo |
    | ---------------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | Nome| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | título| user.jobtitle|

    > [!NOTE]
    > “grupos” refere-se aos grupos de segurança no Appneta que são mapeados como “Função” no Azure AD. Consulte [ este documento ](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) que explica como criar funções personalizadas no Microsoft Azure AD.
        
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o logon único](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o namespace em branco.
    
    e. Clique em **OK**.  

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/appneta-tutorial/tutorial_general_400.png)

6. Para configurar o logon único no lado do **AppNeta Performance Monitor**, você precisará enviar os **XML de Metadados** baixados para a equipe de suporte do [AppNeta Performance Monitor](mailto:support@appneta.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/appneta-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/appneta-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/appneta-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/appneta-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Criar um usuário de teste do AppNeta Performance Monitor

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no AppNeta Performance Monitor. O AppNeta Performance Monitor dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o AppNeta Performance Monitor, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AppNeta Performance Monitor.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao AppNeta Performance Monitor, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **o Monitor de desempenho AppNeta Performance Monitor**.

    ![O link de Monitor de desempenho AppNeta Performance Monitor na lista de aplicativos](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AppNeta Performance Monitor no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo seu aplicativo AppNeta Performance Monitor.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

