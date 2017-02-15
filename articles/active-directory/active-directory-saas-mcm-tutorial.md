---
title: "Tutorial: Integração do Azure Active Directory ao MCM | Microsoft Docs"
description: "Saiba como usar o MCM com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f00799d-e3e9-4ba9-ae4a-fbca843ac5db
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51a736c051bef8550662060fdde89fc50e32928a


---
# <a name="tutorial-azure-active-directory-integration-with-mcm"></a>Tutorial: Integração do Azure Active Directory ao MCM
O objetivo desse tutorial é mostrar como integrar o MCM ao Azure AD (Azure Active Directory).

A integração do MCM ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao MCM
* Você pode permitir que seus usuários faça logon automaticamente no MCM (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o MCM, você precisará dos seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do MCM

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

1. Adicionar MCM da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-mcm-from-the-gallery"></a>Adicionar MCM da galeria
Para configurar a integração do MCM com o Azure AD, você precisará adicionar o MCM à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o MCM por meio da galeria, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **MCM**.
   
    ![Galeria de aplicativos](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Application gallery")

7. No painel de resultados, selecione **MCM** e clique em **Concluir** para adicionar o aplicativo.
   
    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o MCM, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do MCM é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MCM.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **nome de usuário** no MCM.

Para configurar e testar o logon único do Azure AD com o MCM, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do MCM](#creating-a-mcm-test-user)** - para ter um equivalente de Brenda Fernandes no MCM que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo MCM.

**Para configurar o logon único do Azure AD com o MCM, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **MCM**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configure single sign-on")

2. Na página **Como você deseja que os usuários façam logon no MCM**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3. Na página de diálogo Definir Configurações de Aplicativo, execute as seguintes etapas:
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configure App URL")
   
    a. Na caixa de texto **URL de Logon**, digite: `https://myaba.co.uk/client-access/<company name>/saml.php`.
   
    b. Clique em **Avançar**

4. Na página **Configurar o logon único no MCM**, clique em **Baixar metadados** e salve o arquivo de certificado no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configure Single Sign-On")

5. Para que o SSO seja configurado para seu aplicativo, contate a equipe de suporte do MCM. Anexe o arquivo de metadados baixado e compartilhe-o com a equipe do MCM para configurar o SSO no lado dela.

6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configure Single Sign-On")

7. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Configurar o logon único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-mcm-test-user"></a>Criar um usuário de teste do MCM
Nesta seção, você criará uma usuária chamada Brenda Fernandes no MCM. Trabalhe com a equipe de suporte MCM para adicionar os usuários na plataforma MCM.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do MCM ou APIs fornecidas pelo MCM para provisionar as contas de usuário do AAD.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao FMCM.

![Atribuir usuários](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assign users")

**Para atribuir Brenda Fernandes ao MCM, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuários](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assign users")

2. Na lista de aplicativos, selecione **MCM**.
   
    ![Configurar o logon único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assign users")

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuários](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MCM no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo do MCM.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


