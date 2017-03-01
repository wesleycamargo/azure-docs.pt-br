---
title: "Tutorial: integração do Azure Active Directory com o HPE SaaS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o HPE SaaS."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 314003d6-ca66-4456-88c3-934254d4a9a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: 59939dd678452fc29b1861b70073e6f79c230196
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hpe-saas"></a>Tutorial: Integração do Active Directory do Azure ao HPE SaaS
O objetivo desse tutorial é mostrar como integrar o HPE SaaS ao Azure Active Directory (Azure AD).  
A integração do HPE SaaS ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, você pode controlar quem tem acesso ao HPE SaaS
* Você pode permitir que usuários façam logon automaticamente no HPE SaaS (logon único) com as respectivas contas do AD do Azure
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o HPE SaaS, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do HPE SaaS habilitada para logon único

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

1. Como adicionar o HPE SaaS da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-hpe-saas-from-the-gallery"></a>Como adicionar o HPE SaaS da galeria
Para configurar a integração do HPE SaaS ao AD do Azure, você precisará adicionar o HPE SaaS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HPE SaaS da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **HPE SaaS**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_01.png)
7. No painel de resultados, selecione **HPE SaaS**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o HPE SaaS, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do HPE SaaS é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HPE SaaS.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** no HPE SaaS.

Para configurar e testar o logon único do AD do Azure com o HPE SaaS, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do HPE SaaS](#creating-a-hpesaas-test-user)** : para ter um equivalente de Brenda Fernandes no HPE SaaS que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo HPE SaaS.

**Para configurar o logon único do AD do Azure com o HPE SaaS, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **HPE SaaS**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no HPE SaaS**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo HPE SaaS: **“https://login.saas.hpe.com/msg”**. Os clientes também podem alterar isso para a URL específica do aplicativo.

    b. Clique em **Avançar**.


1. Na página **Configurar logon único no HPE SaaS** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_05.png) 
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Avançar**.
2. Para configurar o SSO para seu aplicativo, entre em contato com sua equipe de suporte do HPE SaaS e envie o arquivo de metadados baixado anexado ao email. Para que eles possam ser configurados para integração de SSO.
3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_09.png) 

1. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
2. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_03.png) 
3. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_04.png) 
4. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
5. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.
6. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_07.png) 
7. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-hpe-saas-test-user"></a>Criar um usuário de teste do HPE SaaS
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no HPE SaaS. Trabalhe com a equipe de suporte do HPE SaaS para adicionar os usuários à conta do HPE SaaS. 

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do HPE SaaS.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao HPE SaaS.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao HPE SaaS, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **HPE SaaS**.
   
    ![Configurar Logon Único](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco HPE SaaS no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo HPE SaaS.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_205.png

