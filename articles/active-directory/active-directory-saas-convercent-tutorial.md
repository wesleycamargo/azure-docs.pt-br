---
title: "Tutorial: Integração do Azure Active Directory com o Convercent | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 187fe8af432d2521e3b9efa59b788280c32692a9
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Tutorial: Integração do Azure Active Directory ao Convercent

Neste tutorial, você aprenderá a integrar o Convercent ao Azure AD (Azure Active Directory).

A integração do Convercent ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Convercent
- Você pode permitir que seus usuários façam logon automaticamente no Convercent usando SSO (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Convercent, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Convercent habilitada para SSO

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Convercent da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-convercent-from-the-gallery"></a>Adicionar o Convercent da galeria
Para configurar a integração do Convercent ao Azure AD, você precisará adicionar o Convercent à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Condeco da galeria, realize as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Convercent**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. No painel de resultados, selecione **Convercent** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o SSO do Azure AD com o Convercent, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Convercent é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Convercent.

Essa relação de vinculação é estabelecida por meio da atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Convercent.

Para configurar e testar o SSO do Azure AD com o Convercent, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste Convercent](#creating-a-works-mobile-test-user)**: para ter um equivalente de Brenda Fernandes no Convercent que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o SSO do Azure AD no novo Portal do Azure e configurar o logon único em seu aplicativo Convercent.

**Para configurar o logon único do Azure AD com o Convercent, execute as seguintes etapas:**

1. No novo Portal do Azure, na página de integração de aplicativos do **Convercent**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na página da caixa de diálogo **Logon único**, como **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. Na seção **Domínio e URLs do Convercent**, se você quiser configurar o aplicativo em **Modo iniciado pelo IDP**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)
  1. Na caixa de texto **Identificador**, digite: `https://sts.convercent.com/`
  2. Clique em **"Mostrar configurações de URL avançadas"**.
  3. Na caixa de texto **Estado de retransmissão**, digite: `https://app.convercent.com/`
    
4. Se você quiser configurar o aplicativo em **Modo iniciado pelo SP**, na seção **Domínio e URLs do Convercent**, execute as seguintes etapas:
    
    ![Configurar o logon único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)
  * Na caixa de texto **URL de Entrada**, digite: `https://app.convercent.com/`
    >[!NOTE] 
    >Aqui, sugerimos que você use o identificador exclusivo especificado. Para obter esse valor, entre em contato com a [equipe de suporte do Convercent](mailTo:support@convercent.com).
    >

5. Na seção **Configuração do Convercent**, clique em **Configurar Convercent** para abrir a caixa de diálogo **Configurar logon**. Em seguida, clique em **Metadados XML de SAML** e salve o arquivo de metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Configurar o logon único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. Para que o SSO seja configurado para o aplicativo, entre em contato com a [equipe de suporte do Convercent](mailTo:support@convercent.com) e forneça os **metadados** baixados.

7. No novo Portal do Azure, clique no botão **Salvar**.  
  
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no novo portal chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 
 1. Na caixa de texto **Nome**, digite **Brenda Fernandes**.
 2. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.
 3. Selecione **Mostrar senha** e anote o valor de **senha**.
 4. Clique em **Criar**. 

### <a name="create-a-convercent-test-user"></a>Criar um usuário de teste do Convercent

Nesta seção, você criará um usuário chamado Brenda Fernandes no Convercent. Trabalhe com a [equipe de suporte do Convercent](emailto:support@convercent.com) para adicionar os usuários na plataforma do Convercent.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único (SSO) do Azure concedendo-lhe acesso ao Convercent.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Convercent, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório, vá para **Aplicativos empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Convercent**.

    ![Configurar o logon único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Convercent no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo do Convercent.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
