---
title: "Tutorial: Integração do Azure Active Directory ao Everbridge | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Everbridge."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5608f38de6ae78772499a40c4e2d449cd5efc79c
ms.openlocfilehash: 511a3883cadd9bf2fe1cc39a8626c49fa19a7d8e


---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: integração do Azure Active Directory ao Everbridge
O objetivo desse tutorial é mostrar como integrar o Everbridge ao Azure AD (Azure Active Directory).

A integração do Everbridge ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Everbridge
* Você pode habilitar o logon automático de seus usuários no Everbridge (Logon Único) com as contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Everbridge, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Everbridge com logon único habilitado

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

1. Adicionar Everbridge da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-everbridge-from-the-gallery"></a>Adicionar Everbridge da galeria
Para configurar a integração do Everbridge ao Azure AD, você precisará adicionar o Everbridge da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Everbridge da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Everbridge**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_01.png)
7. Na página de resultados, selecione **Everbridge** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Everbridge, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Everbridge é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Everbridge.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Everbridge.

Para configurar e testar o logon único do Azure AD com o Everbridge, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste Everbridge](#creating-a-everbridge-test-user)** - para ter um equivalente de Brenda Fernandes no Everbridge que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Everbridge.

**Para configurar o logon único do Azure AD com o Everbridge, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Everbridge**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Everbridge**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_03.png) 
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_04.png)
   
    a. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: `https://sso.everbridge.net/{<company name>}`
   
    b. Na caixa de texto **URL de resposta**, digite a URL com o seguinte padrão: `https://manager.everbridge.net/saml/SSO/{<company name>}/alias/defaultAlias`
   
    c. Clique em **Próximo**
4. Na página **Configurar logon único no Everbridge**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_05.png)
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Próximo**.
5. Para configurar o SSO para o aplicativo, você precisa entrar no locatário Everbridge como administrador.
6. No menu na parte superior, clique na guia **Configurações** e selecione **Logon Único** em **Segurança**.
   
    ![Configurar Logon Único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. Na caixa de texto **Nome** , digite o nome do Provedor do Identificador (por exemplo: o nome da sua empresa).
   
    b. Na caixa de texto **Nome da API** , digite o nome da API.
   
    C. Clique no botão **Escolher Arquivo** para carregar o arquivo de metadados que você baixou na **Etapa 4**.
   
    d. Para **Local de Identidade SAML**, selecione "A identidade está no elemento NameIdentifier da instrução Subject".
   
    e. Copie a URL de SSO do SAML do Azure AD para a **URL de Logon do Provedor de Identidade** no Everbridge.
   
    ![Configurar Logon Único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione Redirecionamento HTTP.
7. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_06.png)
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-everbridge-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-everbridge-test-user"></a>Criar um usuário de teste Everbridge
Nesta seção, você criará uma usuária chamada Brenda Fernandes no Everbridge. Trabalhe com a equipe de suporte do Everbridge via <mailto:support@everbridge.com> para adicionar os usuários na plataforma Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Everbridge.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Everbridge, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, escolha **Everbridge**.
   
    ![Configurar Logon Único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Everbridge no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Everbridge.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


