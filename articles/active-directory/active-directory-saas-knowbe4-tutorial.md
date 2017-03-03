---
title: "Tutorial: Integração do Azure Active Directory com o KnowBe4 | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o KnowBe4."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: bc7ce4867180dd9f896b894b56a02bf033ed6bbb
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-knowbe4"></a>Tutorial: Integração do Active Directory do Azure com o KnowBe4
O objetivo deste tutorial é mostrar como integrar o KnowBe4 ao Azure AD (Azure Active Directory).  

A integração do KnowBe4 ao Azure AD proporciona os seguintes benefícios:

* Você pode controlar no AD do Azure quem tem acesso ao KnowBe4
* Você pode permitir que seus usuários façam logon automaticamente no KnowBe4 (logon único) com suas contas do AD do Azure
* Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao KnowBe4, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura com SSO (logon único) do KnowBe4 habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o KnowBe4 da galeria
* Configurar e testar o logon único do AD do Azure

## <a name="add-knowbe4-from-the-gallery"></a>Adicionar o KnowBe4 da galeria
Para configurar a integração do KnowBe4 ao AD do Azure, você precisará adicionar o KnowBe4 da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o KnowBe4 da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **KnowBe4**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_01.png)
7. No painel de resultados, selecione **KnowBe4**, em seguida, clique em **Concluir** para adicionar o aplicativo.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o KnowBe4, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do KnowBe4 é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do KnowBe4.  

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** no KnowBe4.

Para configurar e testar o logon único do AD do Azure com o KnowBe4, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do KnowBe4](#creating-a-KnowBe4-test-user)** – para ter um equivalente de Brenda Fernandes no KnowBe4 vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo KnowBe4.

**Para configurar o logon único do AD do Azure AD com o KnowBe4, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **KnowBe4**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no KnowBe4**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar o logon único](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_04.png) 

  * Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo KnowBe4 usando o seguinte padrão: **“https://\<nome empresa\>.knowbe4.com/auth/saml/aad168.ccsctp.net)”**.

4. Na página **Configurar logon único no KnowBe4** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_05.png) 
   
   1. Clique em **Baixar certificado**e salve o arquivo em seu computador. 
   2. Clique em **Próximo**.
5. Para que o SSO seja configurado para seu aplicativo, contate a [equipe de suporte do KnowBe4](mailto:support@knowbe4.com). Anexe o arquivo de certificado baixado para seu email e compartilhe as URLs de metadados (ID da Entidade, URL de Entrada de Logon Único e URL de Saída) com a equipe do KnowBe4 para configurar o logon único no lado deles.
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_05.png) 
   
   1. Em Tipo de Usuário, selecione Novo usuário na organização.   
   2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_08.png) 
   
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-a-knowbe4-test-user"></a>Criar um usuário de teste do KnowBe4
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no KnowBe4. O KnowBe4 dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o KnowBe4 se ele ainda não existir. 

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do KnowBe4.
>  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao KnowBe4.

    ![Assign User][200] 

**Para atribuir Brenda Fernandes ao KnowBe4, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **KnowBe4**.
   
    ![Configurar Logon Único](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar o logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco KnowBe4 no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo KnowBe4.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_205.png

