---
title: "Tutorial: integração do Azure Active Directory com o Icertis Contract Management Platform | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Icertis Contract Management Platform."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 51c6f95585e2a3c00bfdfa660941ef010b20fe7d
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Tutorial: integração do Azure Active Directory com o Icertis Contract Management Platform
O objetivo deste tutorial é mostrar como integrar o Icertis Contract Management Platform ao Azure AD (Azure Active Directory).

A integração do Icertis Contract Management Platform ao Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD, quem tem acesso ao Icertis Contract Management Platform
* Você pode permitir que seus usuários entrem automaticamente no Icertis Contract Management Platform usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Icertis Contract Management Platform, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura com SSO habilitado do Icertis Contract Management Platform

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar a Plataforma de Gerenciamento de Contratos Icertis da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-icertis-contract-management-platform-from-the-gallery"></a>Adicionar a Plataforma de Gerenciamento de Contratos Icertis da galeria
Para configurar a integração do Icertis Contract Management Platform ao Azure AD, você precisa adicionar o Icertis Contract Management Platform da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Icertis Contract Management Platform da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Icertis Contract Management Platform**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_01.png)
7. No painel de resultados, selecione **Icertis Contract Management Platform** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Icertis Contract Management Platform, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Icertis Contract Management Platform é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Icertis Contract Management Platform.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como o valor de **Nome de usuário** no Icertis Contract Management Platform.

Para configurar e testar o SSO do Azure AD com o Icertis Contract Management Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Icertis Contract Management Platform](#creating-a-icertis-contract-management-platform-test-user)** - para ter um equivalente de Brenda Fernandes no Icertis Contract Management Platform que seja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o SSO do Azure AD no portal clássico e configurará o SSO em seu aplicativo do Icertis Contract Management Platform.

**Para configurar o logon único do Azure AD com o Icertis Contract Management Platform, execute as seguintes etapas:**

1. No Portal clássico, na página de integração de aplicativos do **Icertis Contract Management Platform**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Icertis Contract Management Platform**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_03.png) 
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_04.png)
  1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.icertis.com`
  2. Clique em **Próximo**.

   >[!NOTE]
   >Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada real. Para obter esses valores, entre em contato com o Icertis Contract Management Platform.
   >

1. Na página **Configurar logon único no Icertis Contract Management Platform**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_05.png)
  1. Clique em **Baixar metadados**e salve o arquivo no computador.
  2. Clique em **Próximo**.
2. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do Icertis Contract Management Platform e forneça o seguinte: 
   *  O arquivo de **metadados baixado** 
   *  A  **ID da Entidade** 
   *  A  **URL de SSO do SAML** 
   *  A  **URL do Serviço de Logoff Único**
3. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização. 
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_06.png)
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-icertisicm-tutorial/create_aaduser_08.png)
  1. Anote o valor da **Nova Senha**. 
  2. Clique em **Concluído**.   

### <a name="create-a-icertis-contract-management-platform-test-user"></a>Criar um usuário de teste do Icertis Contract Management Platform
Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no Icertis Contract Management Platform. Trabalhe com a equipe de suporte do Icertis Contract Management Platform para adicionar usuários ao Icertis Contract Management Platform.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Icertis Contract Management Platform.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Icertis Contract Management Platform, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Icertis Contract Management Platform**.
   
    ![Configurar Logon Único](./media/active-directory-saas-icertisicm-tutorial/tutorial_icertisicm_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Icertis Contract Management Platform, você deve ser conectado automaticamente ao aplicativo Icertis Contract Management Platform.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-icertisicm-tutorial/tutorial_general_205.png

