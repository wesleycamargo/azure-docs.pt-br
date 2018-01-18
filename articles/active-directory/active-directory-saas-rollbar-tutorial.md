---
title: "Tutorial: integração do Azure Active Directory com o Rollbar | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Rollbar."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: bb8a81327163513ab721d2ad72da19173b59bc1f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: integração do Azure Active Directory com o Rollbar

Neste tutorial, você aprende como integrar o Rollbar ao Azure AD (Azure Active Directory).

A integração do Rollbar ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem terá acesso ao Rollbar.
- Você pode permitir que usuários façam logon automaticamente no Rollbar (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Rollbar, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Rollbar habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Rollbar da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-rollbar-from-the-gallery"></a>Adicionando o Rollbar da galeria
Para configurar a integração do Rollbar ao Azure AD, você precisará adicionar o Rollbar da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Rollbar da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Rollbar**, selecione **Rollbar** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Rollbar na lista de resultados](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Rollbar com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Rollbar é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Rollbar.

No Rollbar, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Rollbar, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Rollbar](#create-a-rollbar-test-user)** – para ter um equivalente de Brenda Fernandes no Rollbar que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Rollbar.

**Para configurar o logon único do Azure AD com o Rollbar, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Rollbar**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. Na seção **Domínio e URLs do Rollbar**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. Na caixa de texto **Identificador**, digite a URL: `https://saml.rollbar.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://rollbar.com/<accountname>/saml/sso/azure/`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Rollbar](mailto:support@rollbar.com) para obter esses valores. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. Em outra janela do navegador da Web, faça logon em seu site empresarial do Rollbar como um administrador.

8. Clique nas **Configurações de perfil** no canto superior direito e depois em **Configurações de nome de conta**.
    
    ![Configuração](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Em SEGURANÇA, clique em **Provedor de Identidade**.

    ![Configuração](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. Na seção **Provedores de Identidade do SAML**, execute as seguintes etapas:
    
    ![Configuração](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Selecione **AZURE** na lista suspensa **Provedor de Identidade do SAML**.

    b. Abra seu arquivo de metadados no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Metadados do SAML**.

    c. Clique em **Salvar**.

11. Depois de clicar no botão Salvar, a tela ficará assim. Execute as seguintes etapas nesta seção:
    
    ![Configuração](./media/active-directory-saas-rollbar-tutorial/configure3.png)

    a. Marque a caixa de seleção **Requerer logon por meio do Provedor de Identidade do SAML**.

    b. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-rollbar-test-user"></a>Criar um usuário de teste do Rollbar

Para permitir que os usuários do Azure AD façam logon no Rollbar, eles devem ser provisionados no Rollbar. No caso do Rollbar, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site empresarial do Rollbar como administrador.

2. Clique nas **Configurações de perfil** no canto superior direito e depois em **Configurações de nome de conta**.

    ![Usuário](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Clique em **Usuários**.
    
    ![Adicionar Funcionário](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Clique em **Convidar Membros da Equipe**.

    ![Convidar Pessoas](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. Na caixa de texto, digite o nome do usuário, como **brittasimon@contoso.com** e clique em **Adicionar/Convidar**.

    ![Convidar Pessoas](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. O usuário recebe um convite e, depois de aceitá-lo, ele é criado no sistema.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-a acesso ao Rollbar.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Rollbar, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Rollbar**.

    ![O link do Rollbar na Lista de aplicativos](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Rollbar no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Rollbar.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

