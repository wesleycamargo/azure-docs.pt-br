---
title: "Tutorial: Integração do Azure Active Directory ao Birst Agile Business Analytics | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Birst Agile Business Analytics."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c9aa91bb61a097dbe70415e7d118a781ea3c607f
ms.openlocfilehash: 55187dda51da35966148956f8fa8fbc1dce6f0b0
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Tutorial: Integração do Active Directory do Azure ao Birst Agile Business Analytics
O objetivo deste tutorial é mostrar como integrar o Birst Agile Business Analytics ao Azure AD (Azure Active Directory).

A integração do Birst Agile Business Analytics ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no AD do Azure quem tem acesso ao Birst Agile Business Analytics
* Você pode permitir que os usuários façam logon automaticamente no Birst Agile Business Analytics usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Birst Agile Business Analytics, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Birst Agile Business Analytics com SSO habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Birst Agile Business Analytics a partir da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-birst-agile-business-analytics-from-the-gallery"></a>Adicionar o Birst Agile Business Analytics a partir da galeria
Para configurar a integração do Birst Agile Business Analytics ao AD do Azure, você precisará adicionar o Birst Agile Business Analytics à sua lista de aplicativos de SaaS gerenciados a partir da galeria.

**Para adicionar o Birst Agile Business Analytics da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Birst Agile Business Analytics**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/tutorial_birst_01.png)
7. No painel de resultados, selecione **Birst Agile Business Analytics** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/tutorial_birst_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Birst Agile Business Analytics com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o AD do Azure precisa saber qual usuário do Birst Agile Business Analytics é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Birst Agile Business Analytics.

Essa relação de vinculação é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Birst Agile Business Analytics.

Para configurar e testar o logon único do AD do Azure com o Birst Agile Business Analytics, você precisará concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do Birst Agile Business Analytics](#creating-a-birst-agile-business-analytics-test-user)** - para ter um equivalente de Brenda Fernandes no Birst Agile Business Analytics vinculado à representação no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo do Birst Agile Business Analytics.

**Para configurar o SSO do Azure AD com o Birst Agile Business Analytics, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **Birst Agile Business Analytics**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no Birst Agile Business Analytics**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-birst-tutorial/tutorial_birst_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-birst-tutorial/tutorial_birst_04.png) 
  1. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para fazer logon em seu aplicativo Birst Agile Business Analytics usando o seguinte padrão: “https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID”.
   A URL é dependente do datacenter onde sua conta do Birst está localizada. Para datacenters nos EUA, use "https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID" e para datacenters na Europa, use "https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID".
  2. Clique em **Próximo**.
4. Na página **Configurar o logon único no Birst Agile Business Analytics** , execute as seguintes etapas:
   
   ![Configurar o logon único](./media/active-directory-saas-birst-tutorial/tutorial_birst_05.png)   
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
  2. Clique em **Próximo**.
5. Para configurar o SSO de seu aplicativo, entre em contato com a equipe de suporte do Birst Agile Business Analytics por meio do email [info@birst.com](emailTo:info@birst.com) e anexe o arquivo de certificado baixado. Também forneça a URL de SSO de SAML, a URL de Saída e a URL do Emissor para que possam ser configuradas para integração com o SSO.

 >[!NOTE]
 >Mencione para a equipe do Birst que essa integração precisa do Algoritmo SHA256 (o SHA1 não terá suporte) para que eles possam definir o SSO no servidor apropriado, como **app2101** etc.
 >  

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-birst-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-birst-agile-business-analytics-test-user"></a>Criar um usuário de teste do Birst Agile Business Analytics
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Birst Agile Business Analytics. Trabalhe com a equipe de suporte do Birst Agile Business Analytics para adicionar os usuários à conta do Birst. 

>[!NOTE]
>Se você precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Birst Agile Business Analytics.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure concedendo a ela acesso ao Birst Agile Business Analytics.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Birst Agile Business Analytics, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Birst Agile Business Analytics**.
   
    ![Configurar Logon Único](./media/active-directory-saas-birst-tutorial/tutorial_birst_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Birst Agile Business Analytics no Painel de Acesso, você deverá fazer logon automaticamente em seu aplicativo Birst Agile Business Analytics.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-birst-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-birst-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-birst-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-birst-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-birst-tutorial/tutorial_general_205.png

