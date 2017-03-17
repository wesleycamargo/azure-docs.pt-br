---
title: "Tutorial: Integração do Azure Active Directory ao Blackboard Learn | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Blackboard Learn."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0247e7c8f3803e27b4304dae6af11f18432e76b0
ms.openlocfilehash: 92ee9f08ff85a85a08a2d1e021cdb7afec0c6de7
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Tutorial: integração do Azure Active Directory ao Blackboard Learn
Neste tutorial, você aprenderá a integrar o Blackboard Learn ao Azure AD (Azure Active Directory).

A integração do Blackboard Learn ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Blackboard Learn
* Você pode permitir que seus usuários façam logon automaticamente no Blackboard Learn via SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Blackboard Learn, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único (SSO) do Blackboard Learn Cloud Platform

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o Blackboard Learn da galeria
* Configurar e testar o SSO (logon único) do Azure AD

## <a name="add-blackboard-learn-from-the-gallery"></a>Adicionar o Blackboard Learn da galeria
Para configurar a integração do Blackboard Learn ao Azure AD, você precisará adicionar o Blackboard Learn da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Blackboard Learn por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Blackboard Learn**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_01.png)
7. No painel de resultados, selecione **Blackboard Learn** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Blackboard Learn, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Blackboard Learn é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Blackboard Learn.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Blackboard Learn.

Para configurar e testar o logon único do Azure AD o com Blackboard Learn, você precisará realizar as seguintes tarefas básicas:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criação de um usuário de teste do Blackboard Learn](#creating-a-blackboard-learn-test-user)** - para ter um equivalente de Brenda Fernandes no Blackboard Learn que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Blackboard Learn.

O aplicativo Blackboard Learn espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

![Configurar Logon Único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_51.png) 

**Para configurar o logon único do Azure AD com o Blackboard Learn, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Blackboard Learn**, no menu superior, clique em **Atributos**.
   
    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_80.png) 
2. No diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas: mapeamos o Userprincipalname como o atributo de usuário exclusivo aqui, mas você pode mapeá-lo para o valor apropriado que distingue com exclusividade o usuário na organização e que mapeia para o nome de usuário do Blackboard Learn.
   
   | Nome do atributo | Valor do atributo |
   | --- | --- |
   | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    1. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

    ![Configurar Logon Único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_81.png) 
    2. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado nessa linha.
    3. Na lista **Valor do Atributo** , selecione o valor do atributo mostrado nessa linha.
    4. Clique em **Concluído**.    

1. No portal clássico, na página de integração do aplicativo **Blackboard Learn**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no Blackboard Learn**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_04.png) 
    1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no seu aplicativo Blackboard Learn usando o seguinte padrão: **https://\<nome empresa-preço\>.blackboard.com/**.
    2. Clique em **Avançar**.
4. Na página **Configurar logon único no Blackboard Learn** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_05.png)
    1. Clique em **Baixar metadados**e salve o arquivo no computador.
    2. Clique em **Próximo**.
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do Blackboard Learn e forneça o seguinte com os metadados baixados.
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png)   
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_05.png) 
    1. Em Tipo de Usuário, selecione Novo usuário na organização.
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
    3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_06.png) 
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_08.png) 
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-an-blackboard-learn-test-user"></a>Criar um usuário de teste do Blackboard Learn
Nesta seção, você deve criar um usuário chamado Brenda Fernandes no Blackboard Learn. 

O aplicativo Blackboard Learn dá suporte a provisionamento de usuário imediato. Configure as declarações conforme descrito na seção **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Blackboard Learn.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Blackboard Learn, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Blackboard Learn**.
   
    ![Configurar Logon Único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Blackboard Learn do Painel de Acesso, você fará logon automaticamente no aplicativo Blackboard Learn.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_205.png

