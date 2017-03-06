---
title: "Tutorial: Integração do Azure Active Directory ao Tableau Server | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Server."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0849c77c66ac8617e217a69696b5c404be3b5eb4
ms.openlocfilehash: 3aefb8188880fac5c1fcbe237a1e133584089e6a
ms.lasthandoff: 02/03/2017

---

# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory ao Tableau Server
O objetivo deste tutorial é mostrar como integrar o Tableau Server ao Azure AD (Azure Active Directory).

A integração do Tableau Server ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Tableau Server
* Você pode habilitar os usuários a entrar automaticamente no Tableau Server (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local com o Portal Clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Tableau Server, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para SSO (logon único) do Tableau Server

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Tableau Server a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-tableau-server-from-the-gallery"></a>Adição do Tableau Server a partir da galeria
Para configurar a integração do Tableau Server ao Azure AD, você precisa adicionar o Tableau Server da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Server por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Tableau Server**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)
7. No painel de resultados, selecione **Tableau Server** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Tableau Server, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tableau Server é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Server.

Essa relação de vínculo é estabelecida com a atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Tableau Server.

Para configurar e testar o logon único do Azure AD com o Tableau Server, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Tableau Server](#creating-a-tableauserver-test-user)** - para ter um equivalente de Brenda Fernandes no Tableau Server que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Tableau Server.

O aplicativo Tableau Server espera que as declarações SAML estejam em um formato específico. A captura de tela a seguir mostra um exemplo disso. 

![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Para configurar o logon único do Azure AD com o Tableau Server, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Tableau Server**, no menu superior, clique em **Atributos**.
   
    ![Configurar o logon único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 
2. Na caixa de diálogo **Atributos de token SAML** , execute as seguintes etapas:

   1. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 
   2. Na caixa de texto **Nome do Atributo**, digite **nome de usuário**.
   3. Na lista **Valor do Atributo**, selecione **user.displayname**.
   4. Clique em **Concluído**.    

3. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar o logon único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  
4. Clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
5. Na página **Como você deseja que os usuários façam logon no Tableau Server**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 
6. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 

   1. Na caixa de texto **URL de Entrada** , digite a URL do Tableau Server. 
   2. Na **caixa Identificador**, copie a URL.
   3. Clique em **Próximo**.
h
7. Na página **Configurar logon único no Tableau Server**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 

   1. Clique em **Baixar metadados**e salve o arquivo no computador.
   2. Clique em **Próximo**.

8. Para configurar o SSO para o aplicativo, você precisa entrar no locatário Tableau Server como administrador.
   
   1. Na configuração do Tableau Server, clique na guia **SAML** .
  
    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
   2. Marque a caixa de seleção **Usar SAML para logon único**.
   3. Localize o arquivo de Metadados de Federação baixado do portal clássico do Azure e carregue-o no **arquivo de metadados do Idp SAML**.
   4. URL de retorno do Tableau Server — a URL que os usuários do Tableau Server acessarão, por exemplo, http://tableau_server. Não é recomendável usar http://localhost. O uso de URL com barra à direita (por exemplo, http://tableau_server/) não é aceito. Copie a **URL de retorno do Tableau Server** e cole-a na caixa de texto **URL de Logon** do Azure AD, conforme mostrado na etapa 3
   5. ID de entidade SAML - a ID de entidade identifica com exclusividade sua instalação do Tableau Server para o IdP. Você pode digitar a URL do Tableau Server novamente aqui, se desejar, mas ela não precisa ser sua URL do Tableau Server. Copie a **ID de entidade SAML** e cole-a na caixa de texto **IDENTIFICADOR** do Azure AD, conforme mostrado na etapa 3.
   6. Clique em **Exportar Arquivo de Metadados** e abra-o no aplicativo de editor de texto. Localize a URL do Serviço de Declaração do Consumidor com Http Post e Índice 0 e copie a URL. Agora, cole-a na caixa de texto **URL de resposta** do Azure AD, conforme mostrado na etapa 3. 
   7. Clique no botão **OK** na página Configuração do Tableau Server.
   
    >[!NOTE] 
    >Se precisar de ajuda para configurar o SAML no Tableau Server, consulte o artigo [Configurar SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
10. Na página **Confirmação de logon único**, clique em **Concluir**. 
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.

* Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 
   
   1. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   2. Na caixa de texto **Nome do Usuário**, digite **BrendaFernandes**.
   3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 
   
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="creating-a-tableau-server-test-user"></a>Criação de um usuário de teste do Tableau Server
O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Tableau Server. Será necessário provisionar todos os usuários no Tableau Server. Observe também que o nome de usuário deve corresponder ao valor que você configurou no atributo personalizado **username**do Azure AD. Com o mapeamento correto, a integração deve funcionar, conforme [Configuração do Logon Único do Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com o administrador do Tableau Server em sua organização.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo-lhe acesso ao Tableau Server.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tableau Server, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Tableau Server**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tableau Server no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Tableau Server.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

