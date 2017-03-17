---
title: "Tutorial: integração do Azure Active Directory ao Novatus | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o LearnUpon."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 02619192a5106a064a9f97cddac7c20bd73559e7
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Active Directory do Azure com o LearnUpon
O objetivo deste tutorial é mostrar como integrar o LearnUpon ao Azure AD (Azure Active Directory).

A integração do LearnUpon ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, você pode controlar quem tem acesso ao LearnUpon
* Você pode permitir que os usuários façam logon automaticamente no LearnUpon usando SSO (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um local central, no Active Directory do Azure clássico 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao LearnUpon, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do LearnUpon habilitada para SSO (logon único)

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LearnUpon da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="adding-learnupon-from-the-gallery"></a>Adicionando o LearnUpon da galeria
Para configurar a integração do LearnUpon ao AD do Azure, você precisará adicionar o LearnUpon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LearnUpon da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **LearnUpon**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)
7. No painel de resultados, selecione **LearnUpon** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o LearnUpon, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do LearnUpon é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LearnUpon.  

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **nome de usuário** no LearnUpon.

Para configurar e testar o logon único do AD do Azure com o LearnUpon, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do LearnUpon](#creating-a-learnupon-test-user)** - para ter um equivalente de Brenda Fernandes no LearnUpon que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo do LearnUpon.

**Para configurar o logon único do Azure AD com o LearnUpon, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **LearnUpon**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no LearnUpon**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 
  1. Na caixa de texto **URL de resposta**, digite a URL de Serviço do Consumidor de Declaração usando o seguinte padrão: `https://\<companyname\>.learnupon.com/saml/consumer`
  2. Clique em **Próximo**. 
4. Na página **Configurar logon único no LearnUpon** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png)   
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador. Precisaremos desse certificado e das URLs de metadados (ID da Entidade, URL de Entrada e URL de saída de SSO) para configurar o SSO no lado do LearnUpon.
  2. Clique em **Próximo**.
5. Abra outra instância do navegador e faça logon no LearnUpon com uma conta de administrador. 
6. Clique na guia **Configurações** .
   
    ![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 
7. Clique em **Logon Único - SAML** e em **Configurações Gerais** para definir configurações de SAML.
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 
8. Na seção **Configurações Gerais** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  1. Selecione **Habilitado**.
  2. Para **versão**, selecione **2.0**.
  3. Para **Ignorar condições**, selecione **Não**.
  4. Na caixa de texto **Nome do parâmetro Post de Token SAML**, digite o nome do parâmetro da solicitação post para a URL de consumidor SAML indicada acima, que contém a declaração SAML a ser verificada e autenticada. Por exemplo, **SAMLResponse**.
  5. Na caixa de texto **Formato de Nome de Identificador**, digite o valor que indica onde em sua Declaração SAML o identificador de usuários (endereço de Email) reside. Por exemplo: **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
  6. Na caixa de texto **Identificar o Local do Provedor** , digite o valor que indica para onde os usuários são enviados ao clicarem em seu ícone carregado na tela de logon do portal clássico do Azure.
  7.No Portal Clássico do Azure, copie a **URL do Serviço de Logout Único** e cole-a na caixa de texto **URL de Logoff**.
  8. Clique em **Gerenciar impressões digitais**e carregue a impressão digital do certificado baixado. 
9. Clique em **Configurações de Usuário**e execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
  1. Na caixa de texto **Formato do Identificador de Nome**, digite o valor que indica onde em sua declaração SAML reside o nome do usuário. Por exemplo: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname. 
  2. Na caixa de texto **Formato do Identificador de Sobrenome**, digite o valor que indica onde em sua declaração SAML reside o sobrenome do usuário. Por exemplo: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname.
7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.   
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**. 
  2. Clique em **Concluído**.   

### <a name="create-a-learnupon-test-user"></a>Criar um usuário de teste do LearnUpon
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no LearnUpon. O LearnUpon dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o LearnUpon, caso ele ainda não exista. [Configurando o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do LearnUpon. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao LearnUpon.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LearnUpon, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **LearnUpon**.
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando você clicar no bloco LearnUpon no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo LearnUpon.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

