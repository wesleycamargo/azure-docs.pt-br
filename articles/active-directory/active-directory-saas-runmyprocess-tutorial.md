---
title: "Tutorial: integração do Azure Active Directory ao RunMyProcess | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o RunMyProcess."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8aeac420b3848a270a3a9a325c9db0f1569a33e6
ms.openlocfilehash: 2f6bcdd9ea97494d3d12ea4ffd541b4b95a91c4f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Integração do Active Directory do Azure com o RunMyProcess
O objetivo deste tutorial é mostrar como integrar o RunMyProcess ao Azure AD (Azure Active Directory).

A integração do RunMyProcess ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao RunMyProcess
* Você pode permitir que seus usuários façam logon automaticamente no RunMyProcess usando o SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o RunMyProcess, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do RunMyProcess com SSO habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o RunMyProcess da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-runmyprocess-from-the-gallery"></a>Adicionar o RunMyProcess da galeria
Para configurar a integração do RunMyProcess ao Azure AD, você precisa adicionar o RunMyProcess por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RunMyProcess por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **RunMyProcess**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. No painel de resultados, selecione **RunMyProcess** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o RunMyProcess, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do RunMyProcess é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RunMyProcess.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no RunMyProcess.

Para configurar e testar o SSO do Azure AD com o RunMyProcess, conclua os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criação de um usuário de teste do TargetProcess](#creating-a-runmyprocess-test-user)** - para ter um equivalente de Brenda Fernandes no RunMyProcess que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o SSO em seu aplicativo RunMyProcess.

**Para configurar o SSO do Azure AD com o RunMyProcess, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **RunMyProcess**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no RunMyProcess**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
  1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://live.runmyprocess.com/live/<tenant id>`. 
  2. Clique em **Avançar**.
    >[!NOTE]
    >Observe que você precisa atualizar o valor com a URL de Entrada real. Para obter esse valor, entre em contato com a equipe de suporte do RunMyProcess por meio de <mailto:support@runmyprocess.com>.
    >  
4. Na página **Configurar logon único no RunMyProcess**, clique em **Baixar Certificado** e salve o arquivo no computador:
   
    ![Configurar Logon Único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. Em uma janela diferente do navegador da Web, faça logon em seu locatário do RunMyProcess como um administrador.
6. No painel de navegação esquerdo, clique em **Conta** e selecione **Configuração**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. Vá para a seção **Método de autenticação** e execute as etapas a seguir:
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
  1. Em **Método**, selecione **SSO com Samlv2**. 
  2. Na caixa de texto **Emissor SAML**, coloque o valor de **URL de SSO do SAML** do assistente de configuração de aplicativo do Azure AD.
  3. Na caixa de texto **Redirecionamento de logoff**, insira o valor da **URL do Serviço de SSO** do assistente de configuração de aplicativo do Azure AD.
  4. Na caixa de texto **Formato do Id do Nome**, coloque o valor de **Formato do Identificador do Nome** do assistente de configuração de aplicativo do Azure AD.
  5. Copie o conteúdo do arquivo do certificado baixado e cole-o na caixa de texto **Certificado** . 
  6. Clique no ícone **Salvar**.
8. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
9. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
 1. Em Tipo de Usuário, selecione Novo usuário na organização.  
 2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**. 
 3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png)  
 1. Na caixa de texto **Nome**, digite **Brenda**.   
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**. 
 5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png)  
 1. Anote o valor da **Nova Senha**.
 2. Clique em **Concluído**.   

### <a name="create-a-runmyprocess-test-user"></a>Criar um usuário de teste no RunMyProcess
Para permitir que os usuários do AD do Azure façam logon no RunMyProcess, eles devem ser provisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do RunMyProcess como administrador.
2. Clique em **Conta** e selecione **Usuários** no painel de navegação esquerdo, clique em **Novo Usuário**.
   
   ![Novo Usuário](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo Usuário")
3. Na seção **Configurações do Usuário** , realize as seguintes etapas:
   
   ![Perfil](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")   
 1. Digite o **Nome** e o **Email** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas. 
 2. Selecione uma **Linguagem IDE**, um **Idioma** e um **Perfil**. 
 3. Selecione **Enviar email de criação da conta para mim**. 
 4. Clique em **Salvar**.
   
   >[!NOTE]
   >Você pode usar qualquer outra ferramenta de criação da conta de usuário do RunMyProcess ou as APIs fornecidas pelo RunMyProcess para provisionar as contas de usuário do Active Directory do Azure. 
   > 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao RunMyProcess.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao RunMyProcess, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **RunMyProcess**.
   
    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clica no bloco RunMyProcess no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo RunMyProcess.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png

