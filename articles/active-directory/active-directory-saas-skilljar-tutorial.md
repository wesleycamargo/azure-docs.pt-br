---
title: "Tutorial: Integração do Azure Active Directory com o Skilljar | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Skilljar."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c572f556-98a3-48e6-8e4c-e634b7a2ba70
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0dccbd6d38f2d6f73bfb536ba5639c7ec835d43d
ms.openlocfilehash: a38b8092adfb0e30cb5d23ed0f10d35ce99bc5db
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-skilljar"></a>Tutorial: integração do Active Directory do Azure com o Skilljar
O objetivo desse tutorial é mostrar como integrar o Skilljar ao Azure AD (Azure Active Directory).  

A integração do Skilljar ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Skilljar
* Você pode permitir que os usuários façam logon automaticamente no Skilljar usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Skilljar, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Skilljar habilitado para SSO

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Skilljar da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="adding-skilljar-from-the-gallery"></a>Adicionando Skilljar da galeria
Para configurar a integração do Skilljar ao Azure AD, você precisará adicionar o Skilljar da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Skilljar a partir da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Skilljar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_01.png)
7. No painel de resultados, selecione **Skilljar** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Skilljar, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Skilljar é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Skilljar.  

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Skilljar.

Para configurar e testar o logon único do Azure AD com o Skilljar, você precisará concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do Skilljar](#creating-a-skilljar-test-user)** - para ter um equivalente de Brenda Fernandes no Skilljar que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no Portal Clássico do Azure e configurar o logon único (SSO) em seu aplicativo Skilljar.

**Para configurar o SSO do Azure AD com o Skilljar, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Skilljar**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no Skilljar**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_04.png) 
  1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do Skilljar usando o seguinte padrão: *https://\<nome da empresa\>.skilljar.com*.
  2. Clique em **Próximo**.
4. Na página **Configurar logon único no Skilljar** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_05.png)   
  1. Clique em **Baixar metadados**e salve o arquivo no computador.
  2. Copie o valor **Formato do Identificador de Nome** .
  3. Clique em **Próximo**.
5. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte do Skilljar por email, inclua o valor de **Formato do Identificador de Nome** obtido na etapa anterior e anexe o arquivo de metadados baixado.
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skilljar-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-skilljar-test-user"></a>Criar um usuário de teste do Skilljar
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Skilljar. O Skilljar dá suporte ao provisionamento Just-In-Time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Skilljar se ele ainda não existir. 

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Skilljar. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Skilljar.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Skilljar, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Skilljar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Ao clicar no bloco do Skilljar no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Skilljar.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_205.png

