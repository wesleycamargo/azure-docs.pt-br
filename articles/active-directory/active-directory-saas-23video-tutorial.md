---
title: "Tutorial: integração do Azure Active Directory com o 23 Video | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o 23 Video."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a59a0782176f5221bb8b2f590faeee660f4d1101
ms.openlocfilehash: 2ebc4571cb7ff763449f192f5140c79a65d69833
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Tutorial: integração do Active Directory do Azure com o 23 Video
O objetivo deste tutorial é mostrar como integrar o 23 Video ao Azure AD (Azure Active Directory).

A integração do 23 Video ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar no AD do Azure quem tem acesso ao 23 Video 
* Você pode permitir que seus usuários entrem automaticamente no 23 Video por meio do logon único (SSO) usando suas contas do Azure AD

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o 23 Video, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura com SSO (logon único) do 23 Video habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o 23 Video por meio da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="adding-23-video-from-the-gallery"></a>Adicionando o 23 Video por meio da galeria
Para configurar a integração do 23 Video ao AD do Azure, você precisa adicionar o 23 Video por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o 23 Video por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **23 Video**.
   
    ![Aplicativos][5]
7. No painel de resultados, selecione **23 Video** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][25]

## <a name="configuring-and-testing-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o 23 Video, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do 23 Video é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 23 Video.  

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no 23 Video.

Para configurar e testar o SSO do Azure AD com o 23 Video, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do 23 Video](#creating-a-23-video-test-user)** - para ter um equivalente de Brenda Fernandes no 23 Video que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no Portal Clássico do Azure e configurar o logon único (SSO) em seu aplicativo 23 Video.

**Para configurar o SSO do Azure AD com o 23 Video, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **23 Video**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no 23 Video**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][7] 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][8] 
  1. Na caixa de texto **URL de Resposta**, digite a URL usada pelos usuários para fazer logon no seu site do 23 Video (por exemplo: *https://brenda-fernandes.23Video.com/saml/login*).
   
    >[!NOTE]
    >A integração do Active Directory com o SAML 2.0 está disponível para todos os usuários do 23 Video. Entre em contato com o Suporte em [support@23company.com](mailto:support@23company.com) se precisar dos metadados relacionados. 
    > 
 
  2. Clique em **Próximo**.
4. Na página **Configurar logon único no 23 Video** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][9] 
 1. Clique em Baixar certificado e salve o certificado localmente no computador.
 2. Entre em contato com a equipe de suporte do 23 Video via [support@23company.com](mailto:support@23company.com), forneça a eles seu certificado baixado, a **URL do Emissor**, a **URL de Serviço de Logon Único** e a **URL de Logoff Único** e peça que eles configurem o SSO para seu aplicativo 23 Video.    
 3. Clique em **Próximo**.
5. No Portal Clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
6. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  
 1. Em Tipo de Usuário, selecione Novo usuário na organização. 
 2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**. 
 3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)  
 1. Na caixa de texto **Nome**, digite **Brenda**.   
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**.
 5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)  
 1. Anote o valor da **Nova Senha**. 
 2. Clique em **Concluído**.   

### <a name="create-a-23-video-test-user"></a>Criar um usuário de teste do 23 Video
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no 23 Video.

**Para criar um usuário chamado Brenda Fernandes no 23 Video, realize as seguintes etapas:**

1. Faça logon no site da empresa 23 Video como administrador.
2. Vá para **Configurações**.
3. Na seção **Usuários**, clique em **Configurar**.
   
    ![Atribuir usuário][400]
4. Clique em **Adicionar um novo usuário**. 
   
    ![Atribuir usuário][401]
5. Na seção **Convidar alguém para participar deste site** , execute as seguintes etapas:
   
    ![Atribuir usuário][402]
 1. Na caixa de texto **Endereços de email** , digite o endereço de email de Brenda Fernandes no AD do Azure.  
 2. Clique em **Adicionar o usuário**.   

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao 23 Video.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao 23 Video, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **23 Video**.
   
    ![Atribuir usuário][202] 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clica no bloco 23 Video no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo 23 Video.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png





