---
title: "Tutorial: integração do Azure Active Directory ao CS Stars | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o CS Stars."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5704d151-afb8-40a4-b286-8bacd4f279ee
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 015ca80c952110d3289888ed82d7a543be29950c
ms.openlocfilehash: 3519fcb7621aec61ab35c80558bc0da0e3b2c41e


---
# <a name="tutorial-azure-active-directory-integration-with-cs-stars"></a>Tutorial: integração do Active Directory do Azure ao CS Stars
O objetivo deste tutorial é mostrar como integrar o CS Stars ao Azure AD (Azure Active Directory).  
A integração do CS Stars ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao CS Stars
* Você pode habilitar seus usuários a fazerem logon automaticamente no CS Stars (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao CS Stars, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do CS Stars habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>
>

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando CS Stars da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-cs-stars-from-the-gallery"></a>Adicionando CS Stars da galeria
Para configurar a integração do CS Stars ao Azure AD, você precisa adicionar o CS Stars da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o CS Stars da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **CS Stars**.

    ![Aplicativos][5]
7. No painel de resultados, selecione **CS Stars** e clique em **Concluir** para adicionar o aplicativo.

    ![Aplicativos][400]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o CS Stars, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do CS Stars é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do CS Stars.  
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **nome de usuário** no CS Stars.

Para configurar e testar o logon único do Azure AD com o CS Stars, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criando um usuário de teste CS Stars](#creating-a-cs-stars-test-user)** : para ter um equivalente de Brenda Fernandes no CS Stars que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : habilitar Brenda Fernandes a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo CS Stars.

**Para configurar o logon único do Azure AD com o CS Stars, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **CS Stars**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar Logon Único][6]
2. Na página **Como você deseja que os usuários façam logon no CS Stars**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

    ![Logon Único do AD do Azure][7]
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Definir Configurações de Aplicativo][8]

    a. na caixa de texto **URL de Entrada**, digite a URL utilizada pelos usuários para entrar no aplicativo CS Stars (por exemplo: `https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2`).

    > [!NOTE]
    > Se você não souber qual é o valor correto, contate o seu representante Marsh ClearSight.

    b. Clique em **Próximo**.
4. Na página **Configurar logon único no CS Stars**, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![O que é o Azure AD Connect][9]
5. Para habilitar o logon único para o CS Stars, entre em contato com seu representante Marsh ClearSight e envie o arquivo de metadados.
6. No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![O que é o Azure AD Connect][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  

    ![O que é o Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   

### <a name="creating-a-cs-stars-test-user"></a>Criando um usuário de teste CS Stars
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no CS Stars.

Para obter um usuário criado CS Stars, você precisará entrar em contato com seu representante da Marsh ClearSight.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao CS Stars.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao CS Stars, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **CS Stars**.

    ![Atribuir usuário][202]
3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco CS Stars no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo CS Stars.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png



<!--HONumber=Dec16_HO1-->


