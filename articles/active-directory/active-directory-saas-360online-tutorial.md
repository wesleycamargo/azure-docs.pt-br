<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com o 360° Online | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Azure Active Directory e o 360° Online."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="jeedes"/>



# <a name="tutorial:-azure-active-directory-integration-with-360°-online"></a>Tutorial: Integração do Azure Active Directory com o 360° Online


O objetivo desse tutorial é mostrar como integrar o 360° Online ao Azure AD (Azure Active Directory).

A integração do 360° Online ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao 360° Online Server
- Você pode habilitar seus usuários a fazerem logon automaticamente no 360° Online (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao 360° Online, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um locatário do 360° Online


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição de 360° Online da galeria
2. Configurar e testar o logon único do AD do Azure



## <a name="adding-360°-online-from-the-gallery"></a>Adição de 360° Online da galeria
Para configurar a integração do 360° Online ao Azure AD, você precisa adicionar o 360° Online na galeria à sua lista de aplicativos de SaaS gerenciados.


**Para adicionar o 360° Online da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **360° Online**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/tutorial_360online_01.png)

7. No painel de resultados, selecione **360° Online** e clique em **Concluir** para adicionar o aplicativo.
 
    ![Aplicativos](./media/active-directory-saas-360online-tutorial/tutorial_360online_06.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o 360° Online, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do 360° Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no 360° Online.


Para configurar e testar o logon único do Azure AD com o 360° Online, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste do 360° Online](#creating-a-360-online-test-user)** – para ter um equivalente de Brenda Fernandes no 360° Online que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do 360° Online.

**Para configurar o logon único do Azure AD com o 360° Online, execute as seguintes etapas:**


1. No portal clássico do Azure, na página de integração de aplicativos do **360° Online**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único][13] 

2. Na página **Como você deseja que os usuários façam logon no 360° Online**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-360online-tutorial/tutorial_360online_03.png) 

3. Na página de diálogo **Configurar URL do Aplicativo**, execute as seguintes etapas e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-360online-tutorial/tutorial_360online_04.png)

    a. Na caixa de texto **URL de Logon**, digite a URL utilizada pelos usuários para fazer logon em seu aplicativo do 360° Online usando o seguinte padrão: `https://<company name>.public360online.com`

    b. Clique em **Próximo**

4. Na página de diálogo **Configurar URL do Aplicativo**, execute as seguintes etapas e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-360online-tutorial/tutorial_360online_05.png) 

    a. Clique em **Baixar metadados**e salve-o no computador.

    b. Clique em **Próximo**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte do 360° Online usando o endereço [360online@software-innovation.com](mailto:360online@software-innovation.com) e anexe o arquivo de metadados baixado ao email.

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.

    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  

    ![Logon Único do AD do Azure][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_03.png) 


4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_05.png) 

    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.

    b. Na caixa de texto **Nome do Usuário**, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_07.png) 


8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   




### <a name="creating-a-360°-online-test-user"></a>Criar um usuário de teste do 360° Online

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no 360° Online. 

Para obter um usuário em 360° Online., você precisa entrar em contato com sua equipe de suporte do 360° Online via [360online@software-innovation.com](mailto:360online@software-innovation.com).


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela o acesso ao 360° Online.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao 360° Online, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
 
    ![Atribuir usuário][201] 


2. Na lista de aplicativos, selecione **360° Online**.

    ![Configurar Logon Único](./media/active-directory-saas-360online-tutorial/tutorial_360online_50.png) 


1. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco 360° Online no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo 360° Online.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)







<!--Image references-->

[1]: ./media/active-directory-saas-360online-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-360online-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-360online-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-360online-tutorial/tutorial_general_04.png

[10]: ./media/active-directory-saas-360online-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-360online-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-360online-tutorial/tutorial_general_08.png
[13]: ./media/active-directory-saas-360online-tutorial/tutorial_general_09.png
[20]: ./media/active-directory-saas-360online-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-360online-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-360online-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-360online-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-360online-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


