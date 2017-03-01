---
title: "Tutorial: Integração do Azure Active Directory ao BetterWorks | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o BetterWorks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 30dfd46c8133a2a5bb0b32bd5c035260026eec42
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Tutorial: integração do Azure Active Directory ao BetterWorks
O objetivo desse tutorial é mostrar como integrar o BetterWorks ao Azure AD (Azure Active Directory).

A integração do BetterWorks ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você poderá controlar quem tem acesso ao BetterWorks
* Você pode permitir que seus usuários façam logon automaticamente com o SSO (logon único) do BetterWorks com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao BetterWorks, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para SSO (logon único) do BetterWorks

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>  

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o BetterWorks da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-betterworks-from-the-gallery"></a>Adicionar o BetterWorks da galeria
Para configurar a integração do BetterWorks ao Azure AD, você precisará adicionar o BetterWorks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o BetterWorks da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **BetterWorks**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)
7. No painel de resultados, selecione **BetterWorks** e clique em **Concluir** pra adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o BetterWorks baseado em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BetterWorks é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BetterWorks.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de usuário** no BetterWorks.

Para configurar e testar o logon único do Azure AD com o BetterWorks, você precisará executar as seguintes tarefas básicas:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do BetterWorks](#creating-a-betterworks-test-user)** : para ter um equivalente de Brenda Fernandes no BetterWorks que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar logon único do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo BetterWorks.

O aplicativo BetterWorks espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia**"Atributo"**do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Para configurar o logon único do Azure AD com o BetterWorks, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **BetterWorks**, no menu superior, clique em **Atributos**.
   
    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)
2. Na caixa de diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    1. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    2. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
    3. Na lista **Valor do Atributo** , digite a ID do token saml mostrada para essa linha.
    4. Clique em **Concluído**

1. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 
2. Na página **Como você deseja que os usuários façam logon no BetterWorks**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo**, se você quiser configurar o aplicativo no **modo iniciado pelo IDP**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)
   1. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: `https://app.betterworks.com/saml2/metadata/`
   2. Na caixa de texto **URL de Resposta**, digite a URL no seguinte padrão: `https://app.betterworks.com/saml2/acs/`
   3. Clique em **Avançar**

1. Na página de diálogo **Definir Configurações de Aplicativo**, se você quiser configurar o aplicativo no **modo iniciado pelo SP**, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)
    1. Selecione **Mostrar configurações avançadas (opcional)**.
    2. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo BetterWorks usando o seguinte padrão: `https://app.betterworks.com`
    3. Clique em **Próximo**.

1. Na página **Configurar logon único no BetterWorks**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)
    1. Clique em **Baixar metadados**e salve o arquivo no computador.
    2. Clique em **Próximo**.
2. Para ter o SSO configurado para seu aplicativo, contate a equipe de suporte do BetterWorks via <mailto:support@betterworks.com>. Anexe o arquivo de metadados baixado e compartilhe-o com a equipe do BetterWorks para configurar o SSO no lado dela.
3. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
   ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)
    1. Em Tipo de Usuário, selecione Novo usuário na organização.
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
    3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-a-betterworks-test-user"></a>Criar um usuário de teste do BetterWorks
Nesta seção, você criará um usuário chamado Brenda Fernandes no BetterWorks. 

Trabalhe com a equipe de suporte do BetterWorks via <mailto:support@betterworks.com> para adicionar os usuários da plataforma BetterWorks.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao BetterWorks.

   ![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao BetterWorks, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, escolha **BetterWorks**.
   
    ![Configurar o logon único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BetterWorks no Painel de Acesso, você fará logon automaticamente no seu aplicativo BetterWorks.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png

