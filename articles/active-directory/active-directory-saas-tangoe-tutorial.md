---
title: "Tutorial: Integração do Azure Active Directory ao Tangoe Command Premium Mobile| Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Tangoe Command Premium Mobile."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ac82535515f346d43bbceeb274ce61d81ade60b1


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integração do Azure Active Directory ao Tangoe Command Premium Mobile
Neste tutorial, você aprenderá como integrar o Tangoe Command Premium Mobile ao Azure AD (Azure Active Directory).

A integração do Tangoe Command Premium Mobile ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Tangoe Command Premium Mobile
* Você pode habilitar os usuários a entrar automaticamente no Tangoe Command Premium Mobile (Logon Único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Tangoe Command Premium Mobile, você precisa dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura habilitada para logon único do Tangoe Command Premium Mobile

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Tangoe Command Premium Mobile a partir da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>Adição do Tangoe Command Premium Mobile a partir da Galeria
Para configurar a integração do Tangoe Command Premium Mobile ao Azure AD, você precisará adicionar o Tangoe Command Premium Mobile da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tangoe Command Premium Mobile por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Tangoe Command Premium Mobile**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. No painel de resultados, selecione **Tangoe Command Premium Mobile** e clique em **Concluir** para adicionar o aplicativo.

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Tangoe Command Premium Mobile, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tangoe Command Premium Mobile é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tangoe Command Premium Mobile.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **nome de usuário** no Tangoe Command Premium Mobile.

Para configurar e testar o logon único do Azure AD com o Tangoe Command Premium Mobile, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Tangoe Command Premium Mobile](#creating-an-tangoe-test-user)** - para ter um equivalente de Brenda Fernandes no Tangoe Command Premium Mobile, que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Tangoe Command Premium Mobile.

**Para configurar o logon único do Azure AD com o Tangoe Command Premium Mobile, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Tangoe Command Premium Mobile**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no Tangoe Command Premium Mobile**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no aplicativo Tangoe Command Premium Mobile usando o seguinte padrão: **“https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<tenant issuer\>&Target=\<target page URL\>”**.

    b. Na caixa de texto **URL de Resposta**, digite a URL usando o seguinte padrão: **"https://sso.tangoe.com/sp/ACS.saml2"**

    > [AZURE.NOTE]  Se não souber os valores corretos para as URLs, é possível usar os valores acima como espaços reservados e solicitar os valores corretos ao associado de atendimento ao cliente do Tangoe.


1. Na página **Configurar logon único no Tangoe Command Premium Mobile** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Próximo**.
2. Para que o SSO seja configurado para seu aplicativo, contate seu associado de atendimento ao cliente do Tangoe e forneça o seguinte:

    - O arquivo de metadados baixado
    - A **URL do Emissor**
    - A **URL de SSO do SAML**
    - A **URL do Serviço de Logoff Único**



1. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-tangoe-command-premium-mobile-test-user"></a>Criação de um usuário de teste do Tangoe Command Premium Mobile
Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tangoe Command Premium Mobile. O aplicativo Tangoe Command Premium Mobile precisa que todos os usuários sejam provisionados no aplicativo antes de fazer o Logon Único. Portanto, trabalhe com o associado de atendimento ao cliente do Tangoe para provisionar todos esses usuários para o aplicativo. 

> [!NOTE]
> Se precisar criar um usuário manualmente ou um lote de usuários, entre em contato com a equipe de suporte do Tangoe Command Premium Mobile.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Tangoe Command Premium Mobile.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tangoe Command Premium Mobile, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Tangoe Command Premium Mobile**.

![Configurar Logon Único](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 

1. No menu na parte superior, clique em **Usuários**.

![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Brenda Fernandes**.
2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tangoe Command Premium Mobile no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Tangoe Command Premium Mobile.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


