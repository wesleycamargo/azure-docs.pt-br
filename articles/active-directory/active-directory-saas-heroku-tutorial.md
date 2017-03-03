---
title: "Tutorial: Integração do Azure Active Directory com o Heroku | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Heroku."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 3e09a2069c3342cce78b0d8bcaca310b3de2af08
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Tutorial: Integração do Azure Active Directory ao Heroku
Neste tutorial, você aprenderá como integrar o Heroku ao Azure AD (Azure Active Directory).

A integração do Heroku ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Heroku
* Você pode permitir que seus usuários façam logon automaticamente no SSO (logon único) do Heroku com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Heroku, você precisa dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura com SSO (logon único) do Heroku habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o Heroku da galeria
* Configurar e testar o logon único do AD do Azure

## <a name="add-heroku-from-the-gallery"></a>Adicionar o Heroku da galeria
Para configurar a integração do Heroku ao Azure AD, você precisa adicionar o Heroku da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Heroku da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Heroku**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_01.png)
7. No painel de resultados, escolha **Heroku** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Heroku, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Heroku é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Heroku.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Heroku.

Para configurar e testar o logon único do Azure AD com o Heroku, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criação de um usuário de teste do Heroku](#creating-an-heroku-test-user)** : para ter um equivalente de Brenda Fernandes no Heroku que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Heroku.

**Para configurar o logon único do Azure AD com o Heroku, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Heroku**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Heroku**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_04.png) 
   
   >[!NOTE]
   >Se você não sabe quais são os valores corretos para a URL de Entrada e a URL do Identificador, confira "[Para habilitar o SSO no Heroku, execute as seguintes etapas](#x123)" para obter instruções sobre como obtê-los.   
   >

    1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no aplicativo Heroku usando o seguinte padrão: **"https://sso.heroku.com/saml/\<nome da empresa\>/init"**. 
    2. Na caixa de texto **Identificador**, digite uma URL com o seguinte padrão: "**https://sso.heroku.com/saml/\<nome da empresa\>**".  
    3. Clique em **Próximo**.

1. Na página **Configurar logon único no Heroku** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_05.png) 
    1. Clique em **Baixar metadados**e salve o arquivo no computador.
    2. Clique em **Próximo**.
2. Para habilitar o SSO no Heroku, execute as seguintes etapas:
   
    1. Faça logon na conta do Heroku como administrador.
    2. Clique na guia **Configurações** .
    3. Na **Página de Logon Único**, clique em **Carregar Metadados**.
    4. Carregue o arquivo de metadados que você baixou do portal clássico do Azure.
    5. Quando a instalação for bem-sucedida, os administradores verão um diálogo de confirmação e a URL do Logon de SSO para usuários finais será exibida.
    6. <a name="x123"></a>Copie a **URL de Logon do Heroku** e a **ID da Entidade Heroku** e, no portal clássico do Azure AD, volte para a página **Definir Configurações do Aplicativo** e cole os valores nas caixas de texto relacionadas.

    ![Configurar Logon Único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 
    7. Clique em **Próximo**.

1. Selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_05.png) 
    1. Em Tipo de Usuário, selecione Novo usuário na organização.
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
    3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_06.png) 
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-heroku-tutorial/create_aaduser_08.png) 
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-an-heroku-test-user"></a>Criar um usuário de teste do Heroku
Nesta seção, você criará um usuário chamado Brenda Fernandes no Heroku. O Heroku dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado ao acessar o Heroku se ele ainda não existir. Depois que a conta for provisionada, o usuário final receberá um email de verificação e precisará clicar no link de confirmação.

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará entrar em contato com a equipe de suporte do Heroku.
>  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Heroku.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Heroku, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Heroku**.
   
    ![Configurar Logon Único](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Quando você clicar no bloco Heroku no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo Heroku.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_205.png

