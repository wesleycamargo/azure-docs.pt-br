---
title: "Tutorial: Integração do Azure Active Directory ao Edcor | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Edcor."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5b06f2d8-9cd7-498d-bdd6-88570a0a0a15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 6651e7ab19434cfd8c98afa33908e9aab7034fde
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-edcor"></a>Tutorial: Integração do Azure Active Directory ao Edcor

Neste tutorial, você aprende a integrar o Edcor ao Azure Active Directory.

A integração do Edcor ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem terá acesso ao Edcor.
- Você pode permitir que seus usuários entrem automaticamente no Edcor (Logon Único) com suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure Active Directory ao Edcor, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Edcor

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Edcor da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-edcor-from-the-gallery"></a>Adicionando o Edcor da galeria
Para configurar a integração do Edcor ao Azure Active Directory, você precisará adicionar o Edcor da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Edcor da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Edcor**, selecione **Edcor** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Edcor na lista de resultados](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o Edcor, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Edcor é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no Edcor.

No Edcor, atribua o valor do **nome de usuário** no Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure Active Directory com o Edcor, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Edcor](#create-an-edcor-test-user)** – para ter um equivalente de Brenda Fernandes no Edcor que esteja vinculado à representação de usuário do Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo do Edcor.

**Para configurar o logon único do Azure Active Directory com o Edcor, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Edcor**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_samlbase.png)

3. Na seção **URLs e Domínio do Edcor**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Edcor](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_url1.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://sso.edcor.com/sp/ACS.saml2`


4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-edcor-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Edcor**, clique em **Configurar Edcor** para abrir a janela **Configurar logon**. Copie a **URL de Logoff e a ID da Entidade do SAML** da **seção Referência Rápida.**

    ![Configuração do Edcor](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_configure.png) 

7. Para configurar logon único no lado do **Edcor**, é necessário enviar o **XML de metadados**, a **URL de logout** e a **ID da entidade SAML** à [equipe de suporte do Edcor](http://www.edcor.com/contact-us-2/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-edcor-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-edcor-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-edcor-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-edcor-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-edcor-test-user"></a>Criar um usuário de teste do Edcor

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Edcor. Trabalhe com a [equipe de suporte do Edcor](http://www.edcor.com/contact-us-2/) para adicionar os usuários na plataforma do Edcor. Os usuários devem ser criados e ativados antes de usar o logon único.  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Edcor.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Edcor, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Edcor**.

    ![O link do Edcor na lista de Aplicativos](./media/active-directory-saas-edcor-tutorial/tutorial_edcor_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Edcor no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de Edcor.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-edcor-tutorial/tutorial_general_203.png

