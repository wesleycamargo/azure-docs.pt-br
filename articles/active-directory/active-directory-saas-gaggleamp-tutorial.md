---
title: "Tutorial: integração do Azure Active Directory com o GaggleAMP | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o GaggleAMP."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9cc1a4b7-964b-406b-9e0c-05cb1a7c9856
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a1547627558fde4dd2826ed4e40e4d1c90948034


---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Tutorial: Integração do Active Directory do Azure com o GaggleAMP
O objetivo desse tutorial é mostrar como integrar o GaggleAMP ao Azure AD (Azure Active Directory).

A integração do GaggleAMP ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao GaggleAMP
* Você pode permitir seus usuários façam logon automaticamente no GaggleAMP (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao GaggleAMP, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do GaggleAMP com logon único habilitado

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

1. Adição do GaggleAMP da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-gaggleamp-from-the-gallery"></a>Adição do GaggleAMP da galeria
Para configurar a integração do GaggleAMP com o Azure AD, você precisa adicionar o GaggleAMP à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o GaggleAMP a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **GaggleAMP**.
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_01.png)
7. No painel de resultados, selecione **GaggleAMP** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_02.png)>

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o GaggleAMP, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do GaggleAMP é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GaggleAMP.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no GaggleAMP.

Para configurar e testar o logon único do Azure AD com o GaggleAMP, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do GaggleAMP](#creating-a-GaggleAMP-test-user)** : para ter um equivalente de Brenda Fernandes no GaggleAMP que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do GaggleAMP.

**Para configurar o logon único do Azure AD com o GaggleAMP, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **GaggleAMP**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no GaggleAMP**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_04.png) 

    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon em seu aplicativo do GaggleAMP usando o seguinte padrão: **“https://secure4.gaggleamp.com”**.

    > [AZURE.NOTE]Entre em contato com a [equipe de vendas do GaggleAMP](mailto:sales@gaggleamp.com) se precisar da **URL de Logon** para seu aplicativo.”

    b. Clique em **Próximo**.


1. Na página **Configurar logon único no GaggleAMP** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_05.png) 
   
    a. Clique em **Baixar certificado**e salve o certificado no computador. Precisaremos desse certificado e das URLs de metadados (ID da Entidade, URL de Entrada e URL de saída de SSO) para configurar o SSO no lado do GaggleAMP.
   
    b. Clique em **Próximo**.
2. Em outra instância do navegador, navegue até a página de SSO do SAML criada para você pela equipe de suporte do Gaggle (por exemplo: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).
3. Na página **SSO de SAML** , execute as seguintes etapas:  
   
    ![Logon único do GaggleAMP](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_06.png) 
   
    a. No portal clássico do Azure, copie a **URL do Emissor** e cole-a na caixa de texto **URL do Provedor de Identidade**. 
   
    b. No portal clássico do Azure, copie a **URL do Serviço de Logon Único** e cole-a na caixa de texto **URL de Logon Único do Provedor de Identidade**. 
   
    c. Clique em **Salvar**      
   
    d. Envie o certificado baixado à [equipe de vendas do GaggleAMP](mailto:sales@gaggleamp.com).
4. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
5. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-gaggleamp-test-user"></a>Criação de um usuário de teste do GaggleAMP
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no GaggleAMP. O GaggleAMP dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o GaggleAMP, caso ainda não exista. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao GaggleAMP.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao GaggleAMP, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **GaggleAMP**.
    ![Lista do Azure](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco GaggleAMP no Painel de Acesso, você deve ser automaticamente conectado a seu aplicativo GaggleAMP.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


