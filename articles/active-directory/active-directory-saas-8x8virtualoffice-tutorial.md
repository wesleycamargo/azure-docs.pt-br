---
title: 'Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o 8x8 Virtual Office.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office
O objetivo desse tutorial é mostrar como integrar o 8x8 Virtual Office ao Azure AD (Azure Active Directory).

A integração do 8x8 Virtual Office ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você poderá controlar quem tem acesso ao 8x8 Virtual Office
* Você pode permitir que seus usuários façam logon automaticamente no8x8 Virtual Office (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o 8x8 Virtual Office, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do 8x8 Virtual Office com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Microsoft Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o 8x8 Virtual Office da galeria
2. Configurar e testar o Logon Único do Microsoft Azure AD

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionando o 8x8 Virtual Office da galeria
Para configurar a integração do 8x8 Virtual Office ao Azure AD, você precisará adicionar o 8x8 Virtual Office à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o 8x8 Virtual Office por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **8x8 Virtual Office**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. No painel de resultados, selecione **8x8 Virtual Office** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)

## <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar e testar o Logon Único do Microsoft Azure AD
O objetivo desta seção é mostrar como configurar e testar o Logon Único do Microsoft Azure AD com o 8x8 Virtual Office, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do 8x8 Virtual Office é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 8x8 Virtual Office.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no 8x8 Virtual Office.

Para configurar e testar o Logon Único do Microsoft Azure AD com o 8x8 Virtual Office, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do Logon Único do Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o Logon Único do Microsoft Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste no 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** - para ter um equivalente de Brenda Fernandes no 8x8 Virtual Office que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o Logon Único do Microsoft Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuração do Logon Único do Microsoft Azure AD
Nesta seção, você habilitará o Logon Único do Microsoft Azure AD no portal clássico e configurará o logon único no aplicativo do 8x8 Virtual Office.

**Para configurar o Logon Único do Microsoft Azure AD com o 8x8 Virtual Office, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **8x8 Virtual Office**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no 8x8 Virtual Office**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)
   
    a. Na caixa de texto **URL de Resposta**, digite: `https://sso.8x8.com/saml2`
   
    b. Clique em **Próximo**
4. Na página **Configurar logon único no 8x8 Virtual Office**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
5. Faça logon no seu locatário do 8x8 Virtual Office como administrador.
6. Selecione **Virtual Office Account Mgr** no painel do aplicativo.
   
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)
7. Selecione a **conta comercial** que deseja gerenciar e clique no botão **Entrar**.
   
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)
8. Clique na guia **Accounts** na lista de menus.
   
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)
9. Clique em **Single Sign On** na lista Accounts.
   
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)
10. Selecione **Logon Único** em Método de autenticação e clique em **SAML**.
    
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)
11. Copie a **URL de SSO de SAML**, a **URL de Serviço de Logoff Único** e a **URL do Emissor** do Azure AD para **Entrar**, **URL de Logoff** e **URL do Emissor** no 8x8 Virtual Office. 
    
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)
12. Clique no botão **Navegador** para carregar o certificado que você baixou do Azure AD.
13. Clique no botão **Salvar** .
14. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]
15. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-8x8-virtual-office-test-user"></a>Criar um usuário de teste no 8x8 Virtual Office
O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no 8x8 Virtual Office. O 8x8 Virtual Office dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o 8x8 Virtual Office, caso ele ainda não exista. 

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do 8x8 Virtual Office.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure ao conceder a ela acesso ao 8x8 Virtual Office.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao 8x8 Virtual Office, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **8x8 Virtual Office**.
   
    ![Configurar Logon Único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Microsoft Azure AD usando o Painel de Acesso.

Ao clicar no bloco do 8x8 Virtual Office no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do 8x8 Virtual Office.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


