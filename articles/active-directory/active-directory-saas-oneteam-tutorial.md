---
title: "Tutorial: Integração do Azure Active Directory com o Oneteam | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Oneteam."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2e94916c-64ae-4e1a-a8b5-bc6ef7d28c29
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3327faa5864b721fff5d3a34389d214b0ae29a9
ms.openlocfilehash: dab95c2b63e18c2cd21ffb33370ce16404237762
ms.lasthandoff: 12/16/2016


---
# <a name="tutorial-azure-active-directory-integration-with-oneteam"></a>Tutorial: integração do Azure Active Directory com o Oneteam

Neste tutorial, você aprenderá a integrar o Oneteam ao Azure AD (Azure Active Directory).

A integração do Oneteam ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Oneteam
- Você pode habilitar seus usuários a fazerem logon automaticamente no Oneteam (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Oneteam, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Oneteam habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Oneteam da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-oneteam-from-the-gallery"></a>Adicionando Oneteam da galeria
Para configurar a integração do Oneteam ao Azure AD, você precisará adicionar o Oneteam à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Oneteam da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Oneteam**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_001.png)

7. No painel de resultados, selecione **Oneteam** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Oneteam, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Oneteam é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Oneteam.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de Usuário** no Oneteam.

Para configurar e testar o logon único do Azure AD com o Oneteam, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Oneteam](#creating-a-oneteam-test-user)**: para ter um equivalente de Brenda Fernandes no Oneteam que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Oneteam.


**Para configurar o logon único do Azure AD com o Oneteam, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Oneteam**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar Logon Único][6]

2. Na página **Como você deseja que os usuários façam logon no Oneteam**, escolha **Logon único do Azure AD** e clique em **Avançar**.
 
    ![Configurar Logon Único](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_02.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo no **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:

    ![Configurar Logon Único](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_03.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://api.one-team.io/teams/<team name>/auth/saml/issuer`.

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://api.one-team.io/teams/<team name>/auth/saml/callback`.

    c. Clique em **Próximo**.

4. Se quiser configurar o aplicativo no **modo iniciado pelo SP**, na página de diálogo **Definir Configurações do Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_04.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<team name>.one-team.io/`.

    b. Clique em **Próximo**.

    > [!NOTE]
    > Observe que você precisa atualizar esses valores com a URL de Entrada, o Identificador e a URL de Resposta reais. Você pode gerar o tíquete de suporte com Oneteam <a href="https://support.one-team.com/hc/en-us/requests/new">aqui</a> para obter esses valores.

5. Na página **Configurar o logon único no Oneteam**, clique em **Baixar metadados** e salve o arquivo de metadados no computador:

    ![Configurar Logon Único](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_05.png) 

6. Para obter o SSO configurado para o seu aplicativo, você pode gerar o tíquete de suporte com a equipe de suporte de Oneteam <a href="https://support.one-team.com/hc/en-us/requests/new">aqui</a> e fornecê-las com os **metadados** baixados.

7. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.

    ![Logon Único do AD do Azure][10]

8. Na página **Confirmação de logon único**, clique em **Concluir**.  
  
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oneteam-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-a-oneteam-test-user"></a>Criação de um usuário de teste do Oneteam

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Oneteam. O Oneteam dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Oneteam, caso ele ainda não exista.

> [!NOTE]
> Se você precisar criar um usuário manualmente, você poderá gerar o tíquete de suporte com a equipe de suporte de Oneteam <a href="https://support.one-team.com/hc/en-us/requests/new">aqui</a>.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Oneteam.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Oneteam, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Oneteam**.

    ![Configurar o logon único](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203] 

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
    
    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Oneteam no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Oneteam.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_205.png

