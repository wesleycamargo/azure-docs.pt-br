---
title: "Tutorial: integração do Azure Active Directory ao Predictix Assortment Planning | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Predictix Assortment Planning."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d8fefaf3c371a2eb0fb83c59122a7419cff99e79


---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Tutorial: integração do Azure Active Directory ao Predictix Assortment Planning
Neste tutorial, você aprenderá como integrar o Predictix Assortment Planning ao Azure AD (Azure Active Directory).

a integração do Predictix Assortment Planning com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Predictix Assortment Planning
* Você pode permitir que seus usuários façam logon automaticamente no Predictix Assortment Planning (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Predictix Assortment Planning, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada do Predictix Assortment Planning com logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o Logon Único do Microsoft Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Predictix Assortment Planning da galeria
2. Configurar e testar o Logon Único do Microsoft Azure AD

## <a name="adding-predictix-assortment-planning-from-the-gallery"></a>Adicionando o Predictix Assortment Planning da galeria
Para configurar a integração do Predictix Assortment Planning ao Azure AD, você precisará adicionar o Predictix Assortment Planning da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Predictix Assortment Planning da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Predictix Assortment Planning**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)
7. No painel de resultados, selecione **Predictix Assortment Planning** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)

## <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar e testar o Logon Único do Microsoft Azure AD
Nesta seção, você configurará e testará o Logon Único do Microsoft Azure AD com o Predictix Assortment Planning, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Predictix Assortment Planning é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Predictix Assortment Planning.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Predictix Assortment Planning.

Para configurar e testar o Logon Único do Microsoft Azure AD com o Predictix Assortment Planning, conclua os seguintes blocos de construção:

1. **[Configuração do Logon Único do Microsoft Azure AD](#configuring-azure-ad-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o Logon Único do Microsoft Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste Predictix Assortment Planning](#creating-a-predictix-price-reporting-test-user)** – para ter um equivalente de Brenda Fernandes no Predictix Assortment Planning vinculado à representação dela do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o Logon Único do Microsoft Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuração do Logon Único do Microsoft Azure AD
Nesta seção, você habilitará o Logon Único do Microsoft Azure AD no portal clássico e configurará o logon único em seu aplicativo Predictix Assortment Planning.

**Para configurar o Logon Único do Microsoft Azure AD com o Predictix Assortment Planning, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Predictix Assortment Planning**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Predictix Assortment Planning**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 
   
    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do Predictix Assortment Planning usando o seguinte padrão: **https://\<nome da empresa-preço\>.ap.predictix.com/sso/request**.
   
    b. Clique em **Avançar**
4. Na página **Configurar logon único no Predictix Assortment Planning** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do Predictix Assortment Planning e forneça o seguinte:
   
    • O certificado baixado
   
    • A **ID da Entidade**
   
    • A **URL de SSO do SAML**
   
    • A **URL do Serviço de Logoff Único**
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 
5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-predictix-assortment-planning-test-user"></a>Criando um usuário de teste do Predictix Assortment Planning
Nesta seção, você criará um usuário chamado Brenda Fernandes no Predictix Assortment Planning. Trabalhe com a equipe de suporte do Predictix Assortment Planning para adicionar os usuários à plataforma PredictixAssortment Planning.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Predictix Assortment Planning.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Predictix Assortment Planning, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Predictix Assortment Planning**.
   
    ![Configurar Logon Único](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Microsoft Azure AD usando o Painel de Acesso.

Quando você clica no bloco Predictix Assortment Planning no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo Predictix Assortment Planning.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


