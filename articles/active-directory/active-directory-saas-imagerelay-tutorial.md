---
title: "Tutorial: Integração do Azure Active Directory com o Image Relay | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Image Relay."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 94fdae2992e69f4330d325968a9e66b3f6f38ce0


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Azure Active Directory com o Image Relay
O objetivo desse tutorial é mostrar como integrar o Image Relay ao Azure AD (Azure Active Directory).  
A integração do Image Relay ao Azure AD proporciona os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Image Relay
* Você pode permitir que seus usuários façam logon automaticamente no Image Relay (logon único) com as contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Image Relay, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Image Relay

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

1. Adicionar o Image Relay da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-image-relay-from-the-gallery"></a>Adicionar o Image Relay da galeria
Para configurar a integração do Image Relay ao Azure AD, você precisará adicionar o Image Relay da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Image Relay da galeria, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Image Relay**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. No painel de resultados, selecione **Image Relay** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o ImageRelay com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa de uma conta de usuário que represente o usuário relacionado no Image Relay.  Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Image Relay.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Image Relay.

Para configurar e testar o logon único do Azure AD com o Image Relay, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Image Relay](#creating-a-userecho-test-user)**: para ter um equivalente de Brenda Fernandes no Image Relay que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo Image Relay.

**Para configurar o logon único do Azure AD com o Image Relay, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Image Relay**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
     ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários entrem no Image Relay**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
     ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Image Relay (por exemplo: *https://fabrikam.ImageRelay.com/*).
   
    b. Clique em **Próximo**.
4. Na página **Configurar logon único no Image Relay**, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Próximo**.
5. Em outra janela do navegador, entre em seu site de empresa do Image Relay como administrador.
6. Na barra de ferramentas na parte superior, clique na carga de trabalho **Usuários e Permissões**.
   
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. Clique em **Criar Nova Permissão**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. Na carga de trabalho **Configurações de Logon Único**, marque a caixa de seleção **Este Grupo pode apenas entrar via logon único** e clique em **Salvar**.
   
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. Vá para **Configurações da Conta**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. Vá para a carga de trabalho **Configurações de Logon Único** .
    
     ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. No Portal Clássico do Azure, copie a **URL do Serviço de Logon Único** e cole-a na caixa de texto **URL de Logon**.

    b. No Portal Clássico do Azure, copie a **URL do Serviço de Logoff Único** e cole-a na caixa de texto **URL de Logoff**.

    c. Em **Formato da Id de Nome**, selecione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Em **Opções de Vinculação para Solicitações do Provedor de Serviço (Image Relay)**, selecione **Associação POST**.

    e. Em **Certificado x.509**, clique em **Atualizar Certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto Certificado x.509.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Na seção **Provisionamento do Usuário Just-In-Time**, marque **Habilitar o Provisionamento do Usuário Just-In-Time**.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissão (por exemplo, **SSO Básico**) que tem permissão para entrar somente por meio de logon único.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Clique em **Salvar**.

1. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-image-relay-test-user"></a>Criar um usuário de teste do Image Relay
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Image Relay.

**Para criar um usuário chamado Brenda Fernandes no Image Relay, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Image Relay como administrador.
2. Acesse **Usuários e Permissões** e selecione **Criar Usuário SSO**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. Insira o **Email**, o **Nome**, o **Sobrenome** e a **Empresa** do usuário que você deseja configurar e selecione o grupo de permissão (por exemplo, SSO Básico), que é o grupo que pode se conectar somente por meio de logon único.
   
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Image Relay.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Image Relay, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Image Relay**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco Image Relay no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo Image Relay.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


