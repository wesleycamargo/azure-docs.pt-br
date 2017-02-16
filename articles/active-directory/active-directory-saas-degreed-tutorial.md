---
title: "Tutorial: Integração do Azure Active Directory com o Asset Bank | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Asset Bank."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1eda2d1c-b5e2-4c53-ad46-bbeb91cd119a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 30a01be95bdc04359d8816598f1382505e4fd790


---
# <a name="tutorial-azure-active-directory-integration-with-degreed"></a>Tutorial: integração do Azure Active Directory com o Degreed
O objetivo deste tutorial é mostrar como integrar o Degreed ao Azure AD (Azure Active Directory).  
A integração do Degreed ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar  quem tem acesso ao Degreed
* Você pode permitir que usuários façam logon automaticamente no Degreed (Logon Único) com as respectivas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Degreed, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Degreed

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

1. Adição do Degreed a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-degreed-from-the-gallery"></a>Adição do Degreed a partir da galeria
Para configurar a integração do Degreed ao Azure AD, você precisa adicionar o Degreed por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Degreed da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Degreed**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_01.png)
7. No painel de resultados, selecione **Degreed**, em seguida, clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Degreed, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Degreed é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Degreed.

Para configurar e testar o logon único do Azure AD com o Degreed, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar seus usuários para usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Degreed](#creating-a-degreed-test-user)** - para ter um equivalente de Brenda Fernandes no Degreed que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Degreed.

**Para configurar o logon único do Azure AD com o Degreed, realize as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **Degreed**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no Degreed**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar o logon único](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_04.png) 

    a. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para fazer logon em seu aplicativo do Showpad usando o seguinte padrão: `https://degreed.com/?orgsso=<company code>`

    b. Clique em **Próximo**.


1. Na página **Configurar logon único no Degreed** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_05.png) 
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Próximo**.
2. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do Degreed por meio do endereço [admin@degreed.com](mailto:admin@degreed.com) e anexe o arquivo de metadados ao email.
3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_05.png) 
   
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   
    b. Na caixa de texto **Nome do Usuário**, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-degreed-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-degreed-test-user"></a>Criar um usuário de teste do Degreed
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Degreed. O Degreed dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Degreed, caso ele ainda não exista.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Degreed.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Degreed.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Degreed, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Degreed**.
   
    ![Configurar Logon Único](./media/active-directory-saas-degreed-tutorial/tutorial_degreed_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco Degreed no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Degreed.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-degreed-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


