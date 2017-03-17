---
title: "Tutorial: Integração do Azure Active Directory com o Nomadesk | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Nomadesk."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d261b776-b48e-45f0-9722-0297adefabb8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: d8dc70a5217d21871d229dece61bb9ad147fa1c2
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-nomadesk"></a>Tutorial: Integração do Active Directory do Azure com o Nomadesk
O objetivo desse tutorial é mostrar como integrar o Nomadesk ao Azure AD (Azure Active Directory).

A integração do Nomadesk ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, você pode controlar quem tem acesso ao Nomadesk
* Você pode permitir que os usuários façam logon automaticamente no Nomadesk usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o Nomadesk, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Nomadesk habilitada para SSO (logon único)

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Nomadesk por meio da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-nomadesk-from-the-gallery"></a>Adicionar o Nomadesk da galeria
Para configurar a integração do Nomadesk ao AD do Azure, você precisa adicionar o Nomadesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Nomadesk por meio da galeria, realize as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Nomadesk**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_01.png)
7. No painel de resultados, selecione **Nomadesk** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Nomadesk, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Nomadesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Nomadesk.

Essa relação de vinculação é estabelecida por meio da atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Nomadesk.

Para configurar e testar o logon único do AD do Azure com o Nomadesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Nomadesk](#creating-a-nomadesk-test-user)** - para ter um equivalente de Brenda Fernandes no Nomadesk vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo Nomadesk.

**Para configurar o logon único do AD do Azure com o Nomadesk, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **Nomadesk**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6]
2. Na página **Como você deseja que os usuários façam logon no Nomadesk**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_04.png)
  * Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para fazer logon em seu aplicativo Nomadesk usando o seguinte padrão: **“https://mynomadesk.com/logon/saml/TENANTID”**. Ao referenciar um nome genérico, **TENANTID** precisará ser substituído por uma ID de locatário real.
4. Na página **Configurar logon único no Nomadesk** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_05.png)
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
  2. Clique em **Próximo**.
5. Para ter o SSO configurado para seu aplicativo, entre em contato com a equipe de suporte do Nomadesk via support@nomadesk.com. Anexe o arquivo de certificado baixado ao seu email e compartilhe as URLs de metadados (ID da Entidade, URL de Entrada de Logon Único e URL de Saída) com a equipe do Nomadesk para configurar o logon único em seu lado.
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_06.png)
 1. Na caixa de texto **Nome**, digite **Brenda**.  
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**.  
 5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-nomadesk-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-nomadesk-test-user"></a>Criar um usuário de teste do Nomadesk
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Nomadesk. O Nomadesk dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Nomadesk, caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Nomadesk. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Nomadesk.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Nomadesk, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Nomadesk**.
   
    ![Configurar Logon Único](./media/active-directory-saas-nomadesk-tutorial/tutorial_nomadesk_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Nomadesk no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo Nomadesk.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-nomadesk-tutorial/tutorial_general_205.png

