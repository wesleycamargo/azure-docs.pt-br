---
title: "Tutorial: Integração do Azure Active Directory com o ZwayamSSO | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ZwayamSSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7754344c-34d6-4764-a368-e1dbfe876c0c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: 62e3d3baa936b37a4d86c923d8ebcfc296f41f9d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zwayamsso"></a>Tutorial: Integração do Azure Active Directory com o ZwayamSSO

Neste tutorial, você aprenderá a integrar o ZwayamSSO ao Azure AD (Azure Active Directory).

A integração do ZwayamSSO ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem terá acesso ao ZwayamSSO.
- Você pode permitir que seus usuários façam logon automaticamente em ZwayamSSO (logon único) com suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure Active Directory ao ZwayamSSO, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ZwayamSSO

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. adicionar o ZwayamSSO da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zwayamsso-from-the-gallery"></a>adicionar o ZwayamSSO da galeria
Para configurar a integração do ZwayamSSO ao Azure Active Directory, você precisará adicionar o ZwayamSSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZwayamSSO da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ZwayamSSO**, selecione **ZwayamSSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ZwayamSSO na lista de resultados](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o ZwayamSSO, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ZwayamSSO é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado do ZwayamSSO.

Para configurar e testar o logon único do Azure Active Directory com o ZwayamSSO, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ZwayamSSO](#create-a-zwayamsso-test-user)** – para ter um equivalente de Brenda Fernandes no ZwayamSSO que esteja vinculado à representação de usuário do Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo ZwayamSSO.

**Para configurar o logon único do Azure Active Directory com o ZwayamSSO, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **ZwayamSSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_samlbase.png)

3. Na seção **Domínio e URLs do ZwayamSSO**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do ZwayamSSO](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://sso.zwayam.com/zwayam-saml/zwayam-saml/saml/login?idp=<SAML Entity ID>`

    b. Na caixa de texto **Identificador**, digite a URL: `https://sso.zwayam.com/zwayam-saml/saml/metadata`

    > [!NOTE] 
    > O valor da URL de logon não é real. O valor da **ID de Entidade SAML** é explicado posteriormente no tutorial.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do ZwayamSSO**, clique em **Configurar ZwayamSSO** para abrir a janela **Configurar logon**. Copie a **ID de Entidade SAML** da **seção Referência Rápida** e cole o valor da **ID de Entidade SAML** na caixa de texto **URL de logon** na seção  **Domínio e URLs do ZwayamSSO**.

    ![Configuração do ZwayamSSO](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_configure.png) 

7. Para configurar o logon único no lado do **ZwayamSSO**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do ZwayamSSO](mailto:opendoors@zwayam.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-zwayamsso-test-user"></a>Criar um usuário de teste do ZwayamSSO

Nesta seção, você criará uma usuária chamada Brenda Fernandes no ZwayamSSO. Trabalhe com a [equipe de suporte do ZwayamSSO](mailto:opendoors@zwayam.com) para adicionar os usuários na plataforma do ZwayamSSO. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ZwayamSSO.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ZwayamSSO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **ZwayamSSO**.

    ![O link do ZwayamSSO na lista de Aplicativos](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco ZwayamSSO no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo ZwayamSSO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_203.png

