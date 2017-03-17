---
title: "Tutorial: integração do Azure Active Directory com o BGS Online | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o BGS Online."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4fd6b29b-1b46-4fd1-9f5e-16b1c9d892cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 6af7b7777a0ea0b0eba3f499b2e34f30d5bc1ea0
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bgs-online"></a>Tutorial: Integração do Azure Active Directory ao BGS Online
Neste tutorial, você aprenderá a integrar o BGS Online ao Azure AD (Azure Active Directory).

A integração do BGS Online ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao BGS Online
* Você pode permitir que usuários façam logon automaticamente no BGS Online usando logon único com as respectivas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao BGS Online, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para SSO (logon único) do BGS Online

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BGS Online da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="adding-bgs-online-from-the-gallery"></a>Adicionando o BGS Online da galeria
Para configurar a integração do BGS Online ao Azure AD, você precisa adicionar o BGS Online da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o BGS Online da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **BGS Online**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_01.png)
7. No painel de resultados, escolha **BGS Online** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
Nesta seção, você vai configurar e testar o SSO do Azure AD com o BGS Online, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do BGS Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no BGS Online.

Essa relação de vinculação é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no BGS Online.

Para configurar e testar o SSO do Azure AD com o BGS Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do BGS Online](#creating-a-bgsonline-test-user)** – para ter um equivalente de Brenda Fernandes no BGS Online que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
Nesta seção, você vai habilitar o SSO do Azure AD no portal clássico e configurar o logon único em seu aplicativo BGS Online.

**Para configurar o SSO do Azure AD com o BGS Online, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **BGS Online**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no BGS Online**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_04.png) 
  1. Na caixa de texto **Identificador**, digite a URL usada pelos usuários para fazer logon no aplicativo BGS Online. No ambiente de produção, use este padrão `https://<company name>.millwardbrown.report` e para o ambiente de teste, use este padrão `https://millwardbrown.marketingtracker.nl/mt5/`
  2. Na caixa de texto **URL de Resposta** do ambiente de produção, use este padrão `https://<company name>.millwardbrown.report/sso/saml/AssertionConsumerService.aspx`, e para o ambiente de teste, use este padrão `https://millwardbrown.marketingtracker.nl/mt5/sso/saml/AssertionConsumerService.aspx`
  3. Clique em **Avançar**.
4. Na página **Configurar logon único no BGS Online**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_05.png)
  * Clique em **Baixar certificado**e salve o arquivo em seu computador.
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a [equipe de suporte](mailTo:bgsdashboardteam@millwardbrown.com) do BGS Online e forneça o seguinte:
  * Os **metadados** baixados
  * A **URL de SSO do SAML**
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_06.png)   
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-an-bgs-online-test-user"></a>Criar um usuário de teste do BGS Online
Nesta seção, você criará um usuário chamado Brenda Fernandes no BGS Online. Trabalhe com a [equipe de suporte](mailTo:bgsdashboardteam@millwardbrown.com) do BGS Online para adicionar usuários na plataforma BGS Online.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao BGS Online.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao BGS Online, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **BGS Online**.
   
    ![Configurar Logon Único](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BGS Online no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo BGS Online.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_205.png

