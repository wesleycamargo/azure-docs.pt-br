---
title: "Tutorial: integração do Azure Active Directory ao PlanMyLeave | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o PlanMyLeave."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 822e25ab6cd94f312429260778ef13d53bbc0b5f
ms.openlocfilehash: e2d1071f08700d54616cd6b10cadbf7359aa479b


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Tutorial: integração do Azure Active Directory com o PlanMyLeave

Neste tutorial, você aprenderá a integrar o PlanMyLeave ao Azure AD (Azure Active Directory).

A integração do PlanMyLeave ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você poderá controlar quem tem acesso ao PlanMyLeave
- Você pode permitir que seus usuários façam logon automaticamente no PlanMyLeave (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o PlanMyLeave, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do PlanMyLeave com logon único habilitado


> [!NOTE] 
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando PlanMyLeave da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-planmyleave-from-the-gallery"></a>Adicionando PlanMyLeave da galeria
Para configurar a integração do PlanMyLeave ao Azure AD, você precisará adicionar o PlanMyLeave da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PlanMyLeave por meio da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **PlanMyLeave**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)
    
7. No painel de resultados, selecione **PlanMyLeave** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o PlanMyLeave, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PlanMyLeave é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no PlanMyLeave.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no PlanMyLeave.

Para configurar e testar o logon único do Azure AD com o PlanMyLeave, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste PlanMyLeave](#creating-a-PlanMyLeave-test-user)** – para ter um equivalente de Brenda Fernandes no PlanMyLeave que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo PlanMyLeave.


**Para configurar o logon único do Azure AD com o PlanMyLeave, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **PlanMyLeave**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
     
    ![Configurar Logon Único][6] 

2. Na página **Como você deseja que os usuários façam logon no PlanMyLeave**, escolha **Logon único do Azure AD** e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png) 

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute a seguintes etapa:

    ![Configurar Logon Único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png) 

    - Na caixa de texto **URL de entrada**, digite a URL usada pelos usuários para fazer logon no seu aplicativo PlanMyLeave usando o seguinte padrão: `https://<company-name>.planmyleave.com/Login.aspx`.
    
    > [!NOTE]
    > Observe que você precisa atualizar esses valores com a URL de Logon real. Para obter esses valores, entre em contato com a [equipe de suporte do PlanMyLeave](emaiLto:support@planmyleave.com).
         
4. Na página **Configurar o logon único no PlanMyLeave**, clique em **Baixar metadados** e salve o arquivo de metadados no computador:

    ![Configurar o logon único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png)
   
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do PlanMyLeave <a href=“mailto:support@planmyleave.com”>aqui</a> e forneça o seguinte:

    • O **arquivo de metadados** baixado
    
    • A **URL de SSO do SAML**
    
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
 
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-a-planmyleave-test-user"></a>Criação de um usuário de teste PlanMyLeave

Nesta seção, você criará uma usuária chamada Brenda Fernandes no PlanMyLeave. Trabalhe com a equipe de suporte PlanMyLeave <a href=“mailto:support@planmyleave.com”>aqui</a> para adicionar os usuários na plataforma PlanMyLeave.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao PlanMyLeave.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao PlanMyLeave, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **PlanMyLeave**.

    ![Configurar o logon único](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco PlanMyLeave no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo PlanMyLeave.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO3-->


