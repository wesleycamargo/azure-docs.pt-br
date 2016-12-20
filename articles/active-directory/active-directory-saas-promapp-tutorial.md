---
title: "Tutorial: integração do Azure Active Directory ao Promapp | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Promapp."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dece2cd1c8645c0a2d19177d433dbfe5d6e3f6cd


---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: integração do Active Directory do Azure com o Promapp
O objetivo deste tutorial é mostrar como integrar o Promapp ao Azure AD (Azure Active Directory).  
A integração do Promapp ao Azure AD proporciona os seguintes benefícios: 

* Você pode controlar no Azure AD quem tem acesso ao Promapp 
* Você pode habilitar seus usuários a fazerem logon automaticamente no Promapp (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Promapp, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Promapp

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

1. Adicionando Promapp da galeria 
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-promapp-from-the-gallery"></a>Adicionando Promapp da galeria
Para configurar a integração do Promapp ao Azure AD, você precisa adicionar o Promapp por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Azure AD por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Promapp**.
   
    ![Aplicativos][5]
7. No painel de resultados, escolha **Promapp** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Promapp, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Promapp é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Promapp.  
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **nome de usuário** no Promapp.

Para configurar e testar o logon único do Azure AD com o Promapp, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste Promapp](#creating-a-halogen-software-test-user)** : para ter um equivalente de Brenda Fernandes no Promapp que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure AD e configurar o logon único em seu aplicativo Promapp.

**Para configurar o logon único do Azure AD com o Promapp, realize as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração de aplicativos do **Promapp**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Promapp**, escolha **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][7] 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Logon único do AD do Azure][8] 
   
     a. Na caixa de texto **URL de Logon**, digite a URL utilizada pelos usuários para entrar no site do Promapp (por exemplo: *https://nomedaempresa.promapp.com/instancename*).

     b. Clique em **Avançar**.

1. Na página **Configurar logon único no Promapp** , realize as seguintes etapas:
   
    ![Logon único do AD do Azure][9] 
   
    a. Clique em Baixar certificado e salve o certificado localmente no computador.
   
    b. Clique em **Avançar**.
2. Faça logon no site da empresa Promapp como administrador. 
3. No menu na parte superior, clique em **Administrador**. 
   
    ![Logon único do AD do Azure][12]
4. Clique em **Configurar**. 
   
    ![Logon único do AD do Azure][13]
5. No diálogo **Segurança** , execute as seguintes etapas:
   
    ![Logon único do AD do Azure][14] 
   
    a. No portal clássico do Azure, na caixa de diálogo **Configurar logon único no Promapp**, copie a **URL de Logon Remoto**, cole-a na caixa de texto **URL de Logon Único** e clique em **Salvar**.
   
    b. Para **SSO - Modo Logon Único**, selecione **Opcional** e clique em **Salvar**.
   
    c. Copie o certificado baixado no bloco de notas, abra o conteúdo do certificado sem a primeira linha (*---BEGIN CERTIFICATE---*) e a última linha (*---END CERTIFICATE---*), cole-o na caixa de texto **Certificado SSO-x. 509** e clique em **Salvar**.
6. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-promapp-test-user"></a>Criar um usuário de teste Promapp
O aplicativo Promapp dá suporte ao provisionamento Just-in-Time.
Isso significa que uma conte de usuário será criada automaticamente se necessário durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Promapp.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Promapp, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Promapp**.
   
    ![Atribuir usuário][202] 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco Promapp no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Promapp.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png



<!--HONumber=Nov16_HO3-->


