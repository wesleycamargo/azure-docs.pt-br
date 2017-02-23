---
title: "Tutorial: Integração do Azure Active Directory com o Pantheon | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Pantheon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 52a42d6705d0d2cfb86865027f8315411d7aece0
ms.openlocfilehash: b14872daab1bddf18d68b1f0fb23fc74c12078ed


---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Tutorial: Integração do Azure Active Directory com o Pantheon

Neste tutorial, você aprenderá a integrar o Pantheon ao Azure AD (Azure Active Directory).

A integração do Pantheon ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Pantheon
- Você pode habilitar os usuários a entrar automaticamente no Pantheon (Logon Único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Pantheon, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Pantheon habilitada para logon único


>[!NOTE] 
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Pantheon da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-pantheon-from-the-gallery"></a>Adição do Pantheon da galeria
Para configurar a integração do Pantheon ao Azure AD, você precisa adicionar o Pantheon por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Pantheon da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Pantheon**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_01.png)

7. No painel de resultados, escolha **Pantheon** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Pantheon, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Pantheon é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Pantheon.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de usuário** no Pantheon.

Para configurar e testar o logon único do Azure AD com o Pantheon, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do Pantheon](#creating-a-pantheon-test-user)**: para ter um equivalente de Brenda Fernandes no Pantheon que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único no aplicativo do Pantheon.


**Para configurar o logon único do Azure AD com o Pantheon, execute as seguintes etapas:**

O aplicativo do Pantheon espera a asserção do SAML em um formato específico, o que exige a definição do valor do atributo NameIdentifier com o endereço de email do usuário. Por padrão, o Azure AD usa o UserPrincipalName para o atributo NameIdentifier. Porém, para uma integração bem-sucedida, você precisa ajustar esse valor para corresponder ao endereço de email do usuário.
A integração funcionará apenas depois de concluir o mapeamento correto.

![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_06.png)
    
1. No portal clássico, na página de integração de aplicativos do **Pantheon**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
     
    ![Configurar Logon Único][6] 

2. Na página **Como você deseja que os usuários entrem no Pantheon**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_03.png) 

3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_04.png) 

    a. Na caixa de texto **Identificador**, digite o URN no seguinte padrão: `urn:auth0:pantheon:<orgname>-SSO`
    
    b. Na caixa de texto **URL de resposta**, digite a URL com o seguinte padrão: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO` 

    c. Clique em **Avançar**
 
4. Na página **Configurar logon único no Pantheon**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_05.png)

    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a [equipe de suporte do Pantheon](https://pantheon.io/docs/getting-support) e forneça as seguintes informações:

    • O **certificado**

    • A **URL do Serviço de Logon Único**

    >[!NOTE] 
    >Você também precisa fornecer as informações de Domínios de Email e a Hora em que deseja habilitar esta conexão. Você pode encontrar mais detalhes sobre isso [aqui](https://pantheon.io/docs/sso-organizations/)

6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
 
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-an-pantheon-test-user"></a>Criando de um usuário de teste do Pantheon

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Pantheon. Siga as etapas a seguir para adicionar o usuário ao Pantheon. 

>[!NOTE] 
>Para o SSO funcionar, o usuário precisa ser criado primeiro no Pantheon.

1. Faça logon no Pantheon com credenciais de administrador.

2. Navegue até a página **Organização** do painel.
 
3. Clique em **Pessoas**.

4. Clique em **Adicionar usuário**.

5. Insira o endereço de email do usuário.

6. Escolha a função do usuário.

7. Clique em **Adicionar usuário**.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Pantheon.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Pantheon, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Pantheon**.

    ![Configurar o logon único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Pantheon no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo do Pantheon.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


