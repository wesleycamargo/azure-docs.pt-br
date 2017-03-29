---
title: "Tutorial: Integração do Azure Active Directory com o PingBoard | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o PingBoard."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d9daa59b1f20a76dfe18390ffdab008245387332
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Azure Active Directory ao PingBoard

Neste tutorial, você aprenderá a integrar o PingBoard ao Azure AD (Azure Active Directory).

A integração do PingBoard ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao PingBoard
- Você pode permitir que os usuários façam logon automaticamente no PingBoard (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PingBoard, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do PingBoard

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar PingBoard da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-pingboard-from-the-gallery"></a>Adicionar PingBoard da galeria
Para configurar a integração do PingBoard ao Azure AD, você precisará adicionar o PingBoard à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o PingBoard da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **PingBoard**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_search.png)

5. No painel de resultados, selecione **PingBoard** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o PingBoard, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PingBoard é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PingBoard.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de usuário** no PingBoard.

Para configurar e testar o logon único do Azure AD com o PingBoard, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do PingBoard](#creating-a-pingboard-test-user)** - para ter um equivalente de Brenda Fernandes no PingBoard que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal de Gerenciamento do Azure e configura o logon único em seu aplicativo PingBoard.

**Para configurar o logon único do Azure AD com o PingBoard, realize as seguintes etapas:**

1. No portal de Gerenciamento do Azure, na página de integração do aplicativo **PingBoard**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_samlbase.png)

3. Na seção **Domínio e URLs do PingBoard**, se você quiser configurar o aplicativo em modo iniciado pelo **IDP**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_url.png)

    a. Na caixa de texto **Identificador**, digite o valor como `http://<entity-id>.pingboard.com/sp`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<entity-id>.pingboard.com/auth/saml/consume`

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Entre em contato com a [equipe de suporte do PingBoard](https://support.pingboard.com/) para obter esses valores. 

4. Marque **Mostrar configurações avançadas de URL**, se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_sp_initiated01.png)

    a. Na caixa de texto **URL de Logon**, digite o valor como: `http://<sub-domain>.pingboard.com/sign_in`
     
5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_certificate.png) 

6. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/tutorial_general_400.png)

7. Para configurar o SSO no lado do PingBoard, abra uma nova janela do navegador e faça logon sua conta do PingBoard. Você deve ser administrador do PingBoard para configurar o logon único.

8. No menu superior, selecione **Aplicativos > Integrações**

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/pingboard_integration.png)

9.    Na página **Integrações**, localize o bloco **"Azure Active Directory"** e clique nele.

    ![Configurar o logon único](./media/active-directory-saas-PingBoard-tutorial/pingboard_aad.png)

10. No modal que vem a seguir, clique em **"Configurar"**

    ![Configurar o logon único](./media/active-directory-saas-PingBoard-tutorial/pingboard_configure.png)

11. Na página seguinte, você notará que a "Integração de SSO do Azure está habilitada.". Abra o arquivo Metadados XML baixado em um bloco de notas e cole o conteúdo em **Metadados IDP**.

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/pingboard_sso_configure.png)

12. O arquivo será validado e, se tudo estiver correto, o logon único estará habilitado

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-pingboard-test-user"></a>Criar um usuário de teste do PingBoard

Para permitir que os usuários do Azure AD façam logon no PingBoard, eles deverão ser provisionados no PingBoard.  
No caso do PingBoard, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do PingBoard como administrador.

2. Clique no botão **"Adicionar Funcionário"** na página **Diretório**.

    ![Adicionar Funcionário](./media/active-directory-saas-PingBoard-tutorial/create_testuser_add.png)

3. Na página do diálogo **“Adicionar Funcionário”**, execute as seguintes etapas.

    ![Convidar Pessoas](./media/active-directory-saas-PingBoard-tutorial/create_testuser_name.png)

    a. Na caixa de texto **Nome Completo**, digite o nome completo de Brenda Fernandes.

    b. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    c. Na caixa de texto **Cargo**, digite o cargo de Brenda Fernandes.

    d. Na lista suspensa **Local**, selecione o local de Brenda Fernandes.
    
    e. Clique em **Adicionar**.    

4. Uma tela de confirmação aparecerá para confirmar a adição do usuário.
    
    ![confirmar](./media/active-directory-saas-PingBoard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao PingBoard.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao PingBoard, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **PingBoard**.

    ![Configurar Logon Único](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco PingBoard no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo PingBoard.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_203.png

