---
title: "Tutorial: Integração do Azure Active Directory ao SkyDesk Email | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SkyDesk Email."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: fcea07a412de7b35931ff95b01fbe1276302f1ea


---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: integração do Azure Active Directory ao SkyDesk Email
O objetivo desse tutorial é mostrar como integrar o SkyDesk Email ao Azure AD (Azure Active Directory).

A integração do SkyDesk Email ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao SkyDesk Email
* Você pode habilitar os usuários a entrar automaticamente no SkyDesk Email (Logon Único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao SkyDesk Email, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do SkyDesk Email

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

1. Adicionar o SkyDesk Email a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar o SkyDesk Email a partir da galeria
Para configurar a integração do SkyDesk Email ao Azure AD, você precisará adicionar o SkyDesk Email da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SkyDesk Email a partir da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **SkyDesk Email**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)
7. No painel de resultados, selecione **SkyDesk Email** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o SkyDesk Email baseado em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SkyDesk Email é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SkyDesk Email.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no SkyDesk Email.

Para configurar e testar o logon único do Azure AD com o SkyDesk Email, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do SkyDesk Email](#creating-a-Skydesk-Email-test-user)** - para ter um equivalente de Brenda Fernandes no SkyDesk Email que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo SkyDesk Email.

**Para configurar o logon único do Azure AD com o SkyDesk Email, realize as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **SkyDesk Email**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no SkyDesk Email**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 

   1. Na caixa de texto da URL de Entrada, digite a URL usada pelos usuários para fazer logon no seu aplicativo SkyDesk Email usando o seguinte padrão: **“https://mail.skydesk.jp/portal/\<company name\>”**.
   2. Clique em **Próximo**.

4. Na página **Configurar logon único no SkyDesk Email**, execute estas etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
    b. Clique em **Próximo**.
5. Para habilitar o SSO no **SkyDesk Email**, execute as seguintes etapas:
   1. Entre na sua conta do SkyDesk Email como administrador.
   2. No menu na parte superior, clique em Configuração e selecione a organização. 
    
    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)  
   3. Clique em Domínios no painel esquerdo.
    
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)
   4. Clique em Adicionar Domínio.
    
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)
   5. Insira seu nome de Domínio e verifique o Domínio.
    
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)
   6. Clique em **Autenticação SAML** no painel esquerdo.
    
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)
6. Na página de diálogo **Autenticação SAML** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
   > [!NOTE]
   > Para usar a autenticação baseada em SAML, você deve configurar um **domínio verificado** ou uma **URL de portal**. Você pode definir a URL de portal com o nome exclusivo.
   > 
   > 
   
    ![Configurar o logon único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

   1. No portal clássico do Azure AD, copie o valor da **URL de SSO de SAML** e cole-o na caixa de texto **URL de Entrada**.
   2. No portal clássico do Azure AD, copie o valor da **URL do Serviço de Saída Única** e cole-o na caixa de texto URL de **Logoff**.
   3. A **URL de Alteração de Senha** é opcional, portanto, deixe-a em branco.
   4. Clique em **Obter Chave de Arquivo** para selecionar o certificado baixado do SkyDesk Email e, em seguida, clique em **Abrir** para carregar o certificado.
   5. Para **Algoritmo**, selecione **RSA**.
   6. Clique em **Ok** para salvar as alterações.

7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**. 
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.  
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 
   
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="creating-a-skydesk-email-test-user"></a>Criar um usuário de teste do SkyDesk Email
Nesta seção, você criará uma usuária chamada Brenda Fernandes no SkyDesk Email.

1. Clique em **Acesso de Usuário** no painel esquerdo do SkyDesk Email e digite seu nome de usuário. 

![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Se você precisar criar usuários em massa, contate a equipe de suporte do SkyDesk Email.
>


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao SkyDesk Email.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SkyDesk Email, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione ** Email**.
   
    ![Configurar Logon Único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 
3. No menu na parte superior, clique em **Usuários**.
![Atribuir usuário][203] 
4. Na lista Usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SkyDesk Email no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo SkyDesk Email.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


