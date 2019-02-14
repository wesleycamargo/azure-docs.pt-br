---
title: 'Tutorial: Integração do Azure Active Directory ao Replicon | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46466ac85a8be1dec273440751489833f12c00db
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166804"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Tutorial: Integração do Azure Active Directory ao Replicon

Neste tutorial, você aprenderá a integrar o Replicon ao Microsoft Azure AD (Azure Active Directory).

A integração do Replicon ao Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem terá acesso ao Replicon.
- Você pode permitir que os usuários façam logon automaticamente no Replicon (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao Replicon, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Replicon

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Replicon a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon a partir da galeria
Para configurar a integração do Replicon ao Microsoft Azure AD, você precisará adicionar o Replicon a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Replicon a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Replicon**, selecione **Replicon** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Replicon na lista de resultados](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Replicon, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do Replicon é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado do Replicon.

No Replicon, atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure AD com o Replicon, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Replicon](#create-a-replicon-test-user)** : para ter um equivalente de Brenda Fernandes no Replicon que esteja vinculado à representação de usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal do Azure e configurará o logon único no aplicativo Replicon.

**Para configurar o logon único do Microsoft Azure AD com o Replicon, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Replicon**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. Na seção **URLs e Domínio do Replicon**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Replicon](./media/replicon-tutorial/tutorial_replicon_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://global.replicon.com/<companyname>`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Replicon](https://www.replicon.com/customerzone/contact-support) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/replicon-tutorial/tutorial_general_400.png)

6. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Replicon como administrador.

7. Para configurar o SAML 2.0, execute as seguintes etapas:

    ![Habilitar autenticação de SAML](./media/replicon-tutorial/ic777805.png "Habilitar autenticação de SAML")

     a. Para exibir a caixa de diálogo **EnableSAML Authentication2**, acrescente o seguinte à URL, após a chave da empresa: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * A seguir, é mostrado o esquema do URL completo: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique em **+** para expandir a seção **v20Configuration**.

   c. Clique em **+** para expandir a seção **metaDataConfiguration**.

   d. Clique em **Escolher Arquivo** para selecionar o arquivo XML de metadados de provedor de identidade e clique em **Enviar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/replicon-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/replicon-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/replicon-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/replicon-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-replicon-test-user"></a>Criar um usuário de teste do Replicon

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Mixpanel.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Em uma janela de navegador da Web, faça logon no site de sua empresa do Replicon como administrador.

2. Vá para **Administração\>Usuários**.

    ![Usuários](./media/replicon-tutorial/ic777806.png "Usuários")

3. Clique em **+Adicionar Usuário**.

    ![Adicionar Usuário](./media/replicon-tutorial/ic777807.png "Adicionar Usuário")

4. Na seção **Perfil de Usuário** , realize as seguintes etapas:

    ![Perfil de usuário](./media/replicon-tutorial/ic777808.png "Perfil de usuário")

     a. Na caixa de texto **Nome de Logon**, digite o endereço de e-mail do Microsoft Azure AD do usuário do Microsoft Azure AD que você deseja provisionar, como **BrittaSimon@contoso.com**.

    b. Para **Tipo de Autenticação**, selecione **SSO**.

    c. Na caixa de texto **Departamento** , digite o departamento do usuário.

    d. Para **Tipo de Funcionário**, selecione **Administrador**.

    e. Clique em **Salvar Perfil do Usuário**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Replicon ou as APIs fornecidas pelo Replicon para provisionar as contas de usuário do Microsoft Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Replicon.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Replicon, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Replicon**.

    ![O link do Replicon na lista de Aplicativos](./media/replicon-tutorial/tutorial_replicon_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Replicon no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Replicon.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
[4]: ./media/replicon-tutorial/tutorial_general_04.png

[100]: ./media/replicon-tutorial/tutorial_general_100.png

[200]: ./media/replicon-tutorial/tutorial_general_200.png
[201]: ./media/replicon-tutorial/tutorial_general_201.png
[202]: ./media/replicon-tutorial/tutorial_general_202.png
[203]: ./media/replicon-tutorial/tutorial_general_203.png
