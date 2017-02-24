---
title: "Tutorial: integração do Azure Active Directory com o People | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o People."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: eb24c4fe5f382a30acc524065f2ee9e3a27ce94f


---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>Tutorial: Integração do Azure Active Directory ao People
O objetivo desse tutorial é mostrar como integrar o People ao Azure AD (Azure Active Directory).

A integração do People ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao People
* Você pode habilitar os usuários a entrar automaticamente no People (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local com o Portal Clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao People, você precisará dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura do People habilitada para SSO (logon único)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do People a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-people-from-the-gallery"></a>Adição do People a partir da galeria
Para configurar a integração do People ao Azure AD, você precisará adicionar o People à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o People por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **People**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)
7. No painel de resultados, selecione **People**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o People, com base em uma usuária de teste chamada "Brenda Fernandes".

Para configurar e testar o logon único do Azure AD com o People, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do People](#creating-a-people-test-user)** - para ter um equivalente de Brenda Fernandes em People, que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo People.

**Para configurar o logon único do Azure AD com o People, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **People**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    [Configurar o Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no People**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_03.png) 
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e, em seguida, clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_04.png) 
   
   1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo People usando o seguinte padrão: **“https://\<nome empresa\>.peoplehr.com/”**. 
   2. Se você não souber a URL do locatário, entre em contato com a equipe de suporte do People no endereço [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) para obtê-la.    3. Na caixa de texto **Identificador** , digite a URL do locatário. 
   4. Na caixa de texto **URL de Resposta**, digite a URL no seguinte padrão: "**https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**".
   5. Clique em **Próximo**.
4. Na página **Configurar logon único no People**, execute as seguintes etapas e clique em **Próximo**:
   
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_05.png) 
   
   1. Clique em **Baixar metadados**e salve o arquivo no computador.
   2. Clique em **Próximo**.
5. Para configurar o SSO para o aplicativo, você precisa entrar no locatário People como administrador.
   
   1. No menu à esquerda, clique em **Configurações**.
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)    
   2. Clique em **"Empresa"**.
   
    ![Configurar o logon único](./media/active-directory-saas-people-tutorial/tutorial_people_002.png) 
    3. Em **"Carregar arquivo de metadados SAML de ‘Logon Único’"**, clique em **Procurar** para carregar o arquivo de metadados baixado.
   
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.
Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste do People no Azure AD, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_05.png) 
   
    1. Em Tipo de Usuário, selecione Novo usuário na organização.
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
    3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-people-tutorial/create_aaduser_08.png) 
   
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="creating-a-people-test-user"></a>Criação de um usuário de teste do People
O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no People. O People não dá suporte ao provisionamento just-in-time. Portanto, é preciso contatar a equipe de suporte do People para criar um usuário manualmente.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo-lhe acesso ao People.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao People, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **People**.
   
    ![Configurar Logon Único](./media/active-directory-saas-people-tutorial/tutorial_people_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.
Ao clicar no bloco People no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo People.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


