---
title: "Tutorial: integração do Azure Active Directory ao FreshGrade | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o FreshGrade."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1055bba6-f4df-462e-bc9b-1ad5ada0f638
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5d1460cd41e9420793e5d54710c16bc22a81a6fb
ms.openlocfilehash: c5f9c082bbf74b8caca6ed6ad075e4d7fae61580
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshgrade"></a>Tutorial: Integração do Active Directory do Azure ao FreshGrade
Neste tutorial, você aprenderá a integrar o FreshGrade ao Azure Active Directory (AD do Azure).

A integração do FreshGrade ao AD do Azure oferece os seguintes benefícios:

* No Azure AD é possível controlar quem tem acesso ao FreshGrade - você pode permitir que os usuários entrem automaticamente no FreshGrade usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao FreshGrade, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do FreshGrade com SSO habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar FreshGrade da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="add-freshgrade-from-the-gallery"></a>Adicionar FreshGrade da galeria
Para configurar a integração do FreshGrade com o AD do Azure, você precisará adicionar o FreshGrade à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o FreshGrade por meio da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **FreshGrade**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_01.png)
7. No painel de resultados, selecione **FreshGrade** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
Nesta seção, você configurará e testará o SSO do Azure AD com o FreshGrade, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do FreshGrade é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do FreshGradey.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor de **Nome de usuário** no FreshGrade.

Para configurar e testar o SSO do Azure AD com o FreshGrade, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do FreshGrade](#creating-a-freshgrade-test-user)**: para ter um equivalente de Brenda Fernandes no FreshGrade que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
Nesta seção, você habilitará o logon único (SSO) do Azure AD no portal clássico e configurará o logon único (SSO) em seu aplicativo FreshGrade.

**Para configurar o SSO do Azure AD com o FreshGrade, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **FreshGrade**, clique em **Configurar logon único para abrir o diálogo** **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no FreshGrade**, selecione **Logon único do AD do Azure** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_04.png) 
  1. Na caixa de texto **URL DE ENTRADA**, digite uma URL usando o seguinte padrão: `https://<your-subdomain>.freshgrade.com` ou `https://<your-subdomain>.onboarding.freshgrade.com`.
  2. Clique em **Avançar**
4. Na página **Configurar logon único no FreshGrade**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_05.png)
  3. Clique em **Baixar metadados**e salve o arquivo no computador.
5. Para configurar o SSO para o aplicativo, contate sua equipe de suporte em [Support@freshgrade.com](mailTo:support@freshgrade.com) e forneça o seguinte:
   
  * Os **metadados baixados**
  * A **URL de SSO do SAML**
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-a-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshgrade-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-freshgrade-test-user"></a>Criar um usuário de teste do FreshGrade
Nesta seção, você criará uma usuária chamado Brenda Fernandes no FreshGrade. Trabalhe com a equipe de suporte do FreshGrade para adicionar os usuários na plataforma do FreshGrade.
Para qualquer problema com a criação de usuários entre em contato com [support@freshgrade.com](mailTo:support@freshgrade.com). 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao FreshGrade.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao FreshGrade, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **FreshGrade**.
   
    ![Configurar Logon Único](./media/active-directory-saas-freshgrade-tutorial/tutorial_freshgrade_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na ferramenta.
6. barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do FreshGrade no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do FreshGrade.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-freshgrade-tutorial/tutorial_general_205.png

