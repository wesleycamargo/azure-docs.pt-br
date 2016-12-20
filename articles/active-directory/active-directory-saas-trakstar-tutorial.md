---
title: "Tutorial: Integração do Azure Active Directory ao Trakstar | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Trakstar."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 411cb8c3-95c6-4138-acf2-ffc7f663e89a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 48488c180aaa0f8650e6fc10c2c2ec96dbe858ac


---
# <a name="tutorial-azure-active-directory-integration-with-trakstar"></a>Tutorial: Integração do Azure Active Directory ao Trakstar
O objetivo desse tutorial é mostrar como integrar o Trakstar ao Azure AD (Azure Active Directory).  
A integração do Trakstar ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, é possível controlar quem tem acesso ao Trakstar
* Você pode permitir que os usuários façam logon automaticamente no Trakstar (logon único) com suas contas do AD do Azure
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Trakstar, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Trakstar

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Trakstar da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-trakstar-from-the-gallery"></a>Adicionando o Trakstar da galeria
Para configurar a integração do Trakstar ao AD do Azure, você precisará adicionar o Trakstar da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trakstar da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Trakstar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_01.png)
7. No painel de resultados, selecione **Trakstar** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Trakstar, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Trakstar é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do AD do Azure e o usuário relacionado no Trakstar.  
Essa relação de vinculação é estabelecida com a atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Trakstar.

Para configurar e testar o logon único do AD do Azure com o Trakstar, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Trakstar](#creating-a-Trakstar-test-user)** - para ter um equivalente de Brenda Fernandes no Trakstar que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do AD do Azure no Portal clássico do Azure e configurar o logon único em seu aplicativo Trakstar.

**Para configurar o logon único do AD do Azure com o Trakstar, realize as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativo do **Trakstar**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Trakstar**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_04.png) 

    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para entrar no aplicativo Trakstar usando o seguinte padrão: **“https://app.trakstar.com/auth/saml/callback?namespace=&lt;name space&gt;”**.

    b. Clique em **Próximo**.

1. Na página **Configurar logon único no Trakstar** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Próximo**.
2. Para que o SSO seja configurado para o aplicativo, contate a equipe de suporte do Trakstar pelo email [integrations@trakstar.com](mailto:integrations@trakstar.com) e forneça o seguinte:
   
   * O arquivo de certificado baixado
   * A **URL do Emissor**
   * A **URL de SSO do SAML**
   * A **URL do Serviço de Logoff Único**
3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakstar-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-trakstar-test-user"></a>Criando um usuário de teste do Trakstar
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Trakstar. Trabalhe com a equipe de suporte do Trakstar para adicionar usuários na conta do Trakstar. 

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Trakstar.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao Trakstar.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Trakstar, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Trakstar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco do Trakstar no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Trakstar.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


