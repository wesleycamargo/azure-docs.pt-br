---
title: "Tutorial: integração do Azure Active Directory ao StatusPage | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o StatusPage."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 3b901bcb08a194f72c3dc75e33f2b94fffea370e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração do Active Directory do Azure com o StatusPage
O objetivo desse tutorial é mostrar como integrar o StatusPage ao Azure AD (Azure Active Directory).

A integração do StatusPage ao Azure AD oferece os seguintes benefícios: 

* No AD do Azure, você pode controlar quem tem acesso ao StatusPage 
* Você pode habilitar o logon automático de seus usuários no SSO (logon único) do StatusPage com as contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o StatusPage, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura com logon único (SSO) do StatusPage habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o StatusPage da galeria 
* Configurar e testar o SSO do Azure AD

## <a name="add-statuspage-from-the-gallery"></a>Adicionando o StatusPage da galeria
Para configurar a integração do StatusPage ao AD do Azure, você precisará adicionar o StatusPage da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o StatusPage da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **StatusPage**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. No painel de resultados, selecione **StatusPage** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o StatusPage, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do StatusPage é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no StatusPage.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no StatusPage.

Para configurar e testar o logon único do AD do Azure com o StatusPage, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste de StatusPage](#creating-a-statuspage-test-user)** - para ter um equivalente de Brenda Fernandes no StatusPage que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo do StatusPage. 

**Para configurar o SSO do Azure AD com o StatusPage, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **StatusPage**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no StatusPage**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   >[!NOTE]
   >Entre em contato com a equipe de suporte do StatusPage em [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)para solicitar os metadados necessários para a configuração do logon único. 
   > 
  1. Nos metadados, copie o valor de Issuer e cole-o na caixa de texto **Identificador** .
  2. Nos metadados, copie o valor a URL de Resposta e cole-a na caixa de texto **URL de Resposta** .
  3. Clique em **Próximo**.

4. Na página **Configurar logon único no StatusPage** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)   
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
  2. Clique em **Próximo**.
5. Em outra janela do navegador, entre em seu site de empresa do StatusPage como administrador.
6. Na barra de ferramentas principal, clique em **Gerenciar Conta**.
   
    ![Configurar Logon Único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
7. Clique na guia **Logon Único** . 
   
    ![Configurar Logon Único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
8. Na página Instalação do SSO, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)  
  1. No Portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no StatusPage**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Destino de SSO**. 
  2. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado** . 
  3. Clique em **Salvar**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
10. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.  
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)  
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  1. Anotar o valor da **Nova Senha**  
  2. Clique em **Concluído**.   

### <a name="create-a-statuspage-test-user"></a>Criar um usuário de teste do StatusPage
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no StatusPage.

O StatusPage dá suporte ao provisionamento de usuário just-in-time. Você já habilitou isso em [Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

**Para criar um usuário chamado Brenda Fernandes no StatusPage, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do StatusPage como administrador.
2. Na parte superior do menu, clique em **Gerenciar Conta**.
3. Clique na guia Membros da Equipe. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. Clique em **Adicionar Membro da Equipe**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. Digite o **Endereço de Email**, **Nome** e **Sobrenome** de um usuário válido que você deseja provisionar nas caixas de texto relacionadas. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. Como **Função**, escolha **Administrador do Cliente**.
7. Clique em **Criar Conta**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao StatusPage.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao StatusPage, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **StatusPage**.
   
    ![Configurar Logon Único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco StatusPage no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo StatusPage.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png







