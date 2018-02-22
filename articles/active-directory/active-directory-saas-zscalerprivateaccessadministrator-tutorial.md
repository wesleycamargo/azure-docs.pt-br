---
title: "Tutorial: integração do Azure Active Directory com o Administrador do Zscaler Private Access | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Administrador do Zscaler Private Access."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: bf0b7cbd8047dfdbc1a4503775e6d36f8e8a67c1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: integração do Azure Active Directory com o Administrador do Zscaler Private Access

Neste tutorial, você aprenderá a integrar o Administrador do Zscaler Private Access ao Azure AD (Azure Active Directory).

A integração do Administrador do Zscaler Private Access ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Administrador do Zscaler Private Access.
- Você pode permitir que seus usuários façam logon automaticamente (logon único) no Administrador do Zscaler Private Access com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao Administrador do Zscaler Private Access, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Administrador do Zscaler Private Access

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Administrador do Zscaler Private Access da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Adicionando o Administrador do Zscaler Private Access da galeria
Para configurar a integração do Administrador do Zscaler Private Access ao Azure AD, você precisará adicionar o Administrador do Zscaler Private Access da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Administrador do Zscaler Private Access da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Administrador do Zscaler Private Access**, selecione **Administrador do Zscaler Private Access** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Administrador do Zscaler Private Access na lista de resultados](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Administrador do Zscaler Private Access, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Administrador do Zscaler Private Access é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Administrador do Zscaler Private Access.

Para configurar e testar o logon único do Azure AD com o Administrador do Zscaler Private Access, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Administrador do Zscaler Private Access](#create-a-zscaler-private-access-administrator-test-user)** – para ter um equivalente de Brenda Fernandes no Administrador do Zscaler Private Access que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo Administrador do Zscaler Private Access.

**Para configurar o logon único do Azure AD com o Administrador do Zscaler Private Access, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo do **Administrador do Zscaler Private Access**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Na seção **Domínio e URLs do Administrador do Zscaler Private Access**, se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Marque **Mostrar configurações de URL avançadas**

    d. Na caixa de texto **RelayState**, digite um valor: `idpadminsso`

4.  Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do Administrador do Zscaler Private Access](https://help.zscaler.com/zpa-submit-ticket) para obter esses valores.
 
5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. Em uma janela diferente do navegador da Web, faça logon no Administrador do Zscaler Private Access como um administrador.

8. Na parte superior, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administrador de Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. No canto superior direito, clique em **Adicionar Configuração de IdP**. 

    ![Addidp do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Na página **Adicionar Configuração de IdP**, execute as seguintes etapas:
 
    ![Idpselect do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Clique em **Selecionar Arquivo** para carregar o arquivo de metadados baixado do Azure AD no campo **Upload de Arquivo de Metadados de IdP**.

    b. Ele lê os **metadados de IdP** do Azure AD e popula todas as informações dos campos, conforme mostrado abaixo.

    ![Idpconfig do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecione **Logon Único** como **Administrador**.

    d. Selecione seu domínio do campo **Domínios**.
    
    e. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Criar um usuário de teste do Administrador do Zscaler Private Access

Para permitir que os usuários do Azure AD façam logon no Administrador do Zscaler Private Access, eles devem ser provisionados no Administrador do Zscaler Private Access. No caso do Administrador do Zscaler Private Access, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa do Administrador do Zscaler Private Access como um administrador.

2. Na parte superior, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administrador de Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Clique em **Administradores** do lado esquerdo do menu.

    ![Administrador do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. No canto superior direito, clique em **Adicionar Administrador**:

    ![Adicionar administrador do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na página **Adicionar Administrador**, realize as seguintes etapas:

    ![Administrador de usuários do Administrador do Zscaler Private Access](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Na caixa de texto **Nome de usuário**, insira o email do usuário como **BrittaSimon@contoso.com**.

    b. Na caixa de texto **Senha**, digite a senha.

    c. Na caixa de texto **Confirmar Senha**, digite a senha.

    d. Selecione **Função** como **Administrador do Zscaler Private Access**.

    e. Na caixa de texto **Email**, insira o email do usuário, como **BrittaSimon@contoso.com**.

    f. Na caixa de texto **Telefone**, digite o número de telefone.

    g. Na caixa de texto **Fuso Horário**, selecione o fuso horário.

    h. Clique em **Salvar**.  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Administrador do Zscaler Private Access.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Administrador do Zscaler Private Access, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Administrador do Zscaler Private Access**.

    ![O link do Administrador do Zscaler Private Access na lista de aplicativos](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Administrador do Zscaler Private Access no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Administrador do Zscaler Private Access.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

