---
title: "Tutorial: Integração do Azure Active Directory com o Tableau Online | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Online."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: de8292e6d3642776fc684ded731f48845de846d4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integração do Azure Active Directory com o Tableau Online
Neste tutorial, você aprenderá a integrar o Tableau Online ao Azure AD (Azure Active Directory).

A integração do Tableau Online ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Tableau Online
* Você pode habilitar os usuários a entrar automaticamente no Tableau Online usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Tableau Online, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do **Tableau Online** habilitada para SSO

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Tableau Online da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="adding-tableau-online-from-the-gallery"></a>Adição do Tableau Online da galeria
Para configurar a integração do Tableau Online ao Azure AD, você precisa adicionar o Tableau Online da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Online por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Tableau Online**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)
7. No painel de resultados, selecione **Tableau Online** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
Nesta seção, você vai configurar e testar o SSO do Azure AD com o Tableau Online, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Tableau Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Online.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Tableau Online.

Para configurar e testar o SSO do Azure AD com o Tableau Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Tableau Online](#creating-a-Tableau-Online-test-user)** - para ter um equivalente de Brenda Fernandes no Tableau Online que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo Tableau Online.

**Para configurar o SSO do Azure AD com o Tableau Online, execute as seguintes etapas:**

1. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar o logon único][6]
2. No portal clássico, na página de integração de aplicativos do **Tableau Online**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][7] 
3. Na página **Como você deseja que os usuários façam logon no Tableau Online**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)
4. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas: 
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)
  1. Na caixa de texto URL de Entrada, digite uma URL usando o seguinte padrão: `https://sso.online.tableau.com`
  2. Clique em **Próximo**.
5. Na página **Configurar o logon único no TableauOnline**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)
6. Selecione a confirmação de configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]
8. Em uma janela de navegador diferente, entre no seu aplicativo Tableau Online. Vá para **Settings** e para **Authentication**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
9. Na seção **Authentication Types** . Marque a caixa de seleção **Single sign-on with SAML** para habilitar o SAML.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)
10. Role para baixo até a seção **Import metadata file into Tableau Online** .  Clique em Browser e importe o arquivo de metadados que você baixou do Azure AD. Em seguida, clique em **Apply**.
   
   ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)
11. Na seção **Match assertions** , insira o nome de declaração do Provedor de Identidade correspondente para endereço de email, nome e sobrenome. Para obter essas informações do Azure AD: 
  1. Volte para o Azure AD. No Portal Clássico do Azure, na página de integração de aplicativos do **Tableau Online**.
  2. No menu na parte superior, clique em **Attributes**. 
  3. Copie o nome para os valores: userprincipalname, givenname e surname.
   
     ![Logon Único do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
  4. Alterne para o aplicativo Tableau Online e defina a seção **Tableau Online Attributes** como a seguir:
     * Email: **mail** ou **userprincipalname**
     * Nome: **givenname**
     * Sobrenome: **surname**
   
   ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**. 
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-tableau-online-test-user"></a>Criar um usuário de teste do Tableau Online
Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tableau Online.

1. No **Tableau Online**, clique em **Configurações** e na seção **Autenticação**. Role para baixo até a seção **Selecionar Usuários** . Clique em **Adicionar Usuários** e em **Inserir Endereços de Email**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Selecione **Adicionar usuários para autenticação de logon único (SSO)**. Na caixa de texto **Inserir Endereços de Email**, adicione britta.simon@contoso.com
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tableau Online.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tableau Online, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Tableau Online**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista Todos os Usuários, escolha **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tableau Online no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Tableau Online.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

