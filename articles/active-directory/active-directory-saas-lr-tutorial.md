---
title: "Tutorial: Integração do Azure Active Directory com lr | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory do Azure e lr."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5715579e-598f-4d2e-970a-107b80b97be4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: f7728c37db286b130a79db1353ee40825e8a4336
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-azure-active-directory-integration-with-lr"></a>Tutorial: Integração do Azure Active Directory com lr

Neste tutorial, você aprenderá como integrar o lr com o Azure Active Directory (Azure AD).

Integrar o lr ao AD do Azure fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao lr.
- Você pode habilitar os usuários a serem conectados do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o lr, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do IR

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar lr a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-lr-from-the-gallery"></a>Adicionar lr a partir da galeria
Para configurar a integração de lr no Azure AD, você precisa adicionar lr a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar lr a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **lr**, selecione **lr** no painel de resultados e clique no botão **Adicionar** botão para adicionar o aplicativo.

    ![Ir na lista de resultados](./media/active-directory-saas-lr-tutorial/tutorial_lr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você irá configurar e testar o logon único do Azure AD com o lr com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual é o usuário correspondente no lr a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no lr precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com lr, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste lr](#create-a-lr-test-user)**  - ter uma duplicata de Britta Simon em lr vinculada à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e irá configurar o logon único no aplicativo lr.

**Para configurar o logon único do Azure AD com lr, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **lr**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-lr-tutorial/tutorial_lr_samlbase.png)

3. Na seção **URLs e domínio de Ir**, execute as seguintes etapas:

    ![Informações sobre o logon único do domínio e URLs do Ir](./media/active-directory-saas-lr-tutorial/tutorial_lr_url.png)

    a.  Na caixa de texto **URL de Logon**, digite uma URL: `https://secure.loginradius.com/login`

    b. Na caixa de texto **Identificador**, digite uma URL: `https://lr.hub.loginradius.com/`
     
    > [!NOTE] 
    > Abra a página da URL do logon. Clique na guia **Logon Único** e digite o **nome do plug-in** fornecido pela [equipe de suporte do Ir](mailto:support@loginradius.com) e, em seguida, clique no botão **Entrar** e você será redirecionado para a página do Azure AD para logon. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-lr-tutorial/tutorial_lr_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-lr-tutorial/tutorial_general_400.png)
    
7. Para configurar o logon único no lado do **lr**, você precisa enviar o **XML de metadados** baixado para a [equipe de suporte do Ir](mailto:support@loginradius.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-lr-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-lr-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-lr-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-lr-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-lr-test-user"></a>Criar um usuário de teste lr

Nesta seção, você criará um usuário chamado Britta Simon no lr. Trabalhe junto com a [equipe de suporte do Ir](mailto:support@loginradius.com) para adicionar os usuários na plataforma lr. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon a usar o logon único do Azure, concedendo acesso ao lr.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao lr, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **lr**.

    ![O link lr na lista de aplicativos](./media/active-directory-saas-lr-tutorial/tutorial_lr_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco de lr no painel de acesso, você deverá ser automaticamente conectado ao seu aplicativo lr.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lr-tutorial/tutorial_general_203.png

