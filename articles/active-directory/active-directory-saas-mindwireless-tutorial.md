---
title: 'Tutorial: Integração do Azure Active Directory com o mindWireless | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 1c08b0fe24e2545a07e3335cc0e0ae87a2c0540c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Tutorial: integração do Azure Active Directory com o mindWireless

Neste tutorial, você aprenderá a integrar o mindWireless ao Azure AD (Azure Active Directory).

A integração do mindWireless ao Microsoft Azure AD oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, você poderá controlar quem tem acesso ao mindWireless.
- Você pode permitir que os usuários façam logon automaticamente no mindWireless (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com o mindWireless, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do mindWireless

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o mindWireless da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-mindwireless-from-the-gallery"></a>Adicionando o mindWireless da galeria
Para configurar a integração do mindWireless ao Microsoft Azure AD, você precisará adicionar o mindWireless da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o mindWireless da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **mindWireless**, selecione **mindWireless** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![mindWireless na lista de resultados](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o mindWireless, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do mindWireless é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no mindWireless.

Para configurar e testar o logon único do Microsoft Azure AD com o mindWireless, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do mindWireless](#create-a-mindwireless-test-user)** – para ter um equivalente de Brenda Fernandes no mindWireless que esteja vinculado à representação de usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no Portal do Azure e configurará o logon único no aplicativo mindWireless.

**Para configurar o logon único do Microsoft Azure AD com o mindWireless, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **mindWireless**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. Na seção **URLs e Domínio do mindWireless**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.mwsmart.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do mindWireless](mailto:sdulloor@mindwireless.com) para obter esses valores.

4. O aplicativo mindWireless espera as declarações do SAML em um formato específico, o que exige adicionar mapeamentos de atributo personalizados à configuração de atributos do token SAML.

5. A captura de tela a seguir mostra um exemplo disso. O nome da declaração sempre será **ID do Funcionário** e o valor dela foi mapeado para user.employeeid, que contém a EmployeeID do usuário. Aqui, o mapeamento de usuário do Microsoft Azure AD para o mindWireless é feito na EmployeeID, mas isso pode ser mapeado para um valor diferente também baseado nas configurações do aplicativo. Você pode trabalhar com a [equipe do mindWireless](mailto:sdulloor@mindwireless.com) primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **EmployeeID**.

    ![Configurar o logon único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
    
    | Nome do atributo | Valor do atributo | Valor de namespace |
    | -------------- | --------------- | ----------------|
    | ID do funcionário | user.employeeid | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.
    
    e. Clique em **OK**.
    
7. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-mindwireless-tutorial/tutorial_general_400.png)

9. Na seção **Configuração do mindWireless**, clique em **Configurar mindWireless** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Para configurar o logon único no lado do **mindWireless**, é necessário enviar o **Certificado (Base64) baixado, a URL do Serviço de Logon Único SAML** e a **ID da Entidade SAML** para a [equipe de suporte do mindWireless](mailto:sdulloor@mindwireless.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-mindwireless-test-user"></a>Criar um usuário de teste no mindWireless

Nesta seção, você criará uma usuária chamada Brenda Fernandes no mindWireless. Trabalhe com [equipe de suporte do mindWireless](mailto:sdulloor@mindwireless.com) para adicionar os usuários na plataforma do mindWireless. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao mindWireless.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao mindWireless, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **mindWireless**.

    ![O link do mindWireless na lista de Aplicativos](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco mindWireless no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo mindWireless.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_203.png

