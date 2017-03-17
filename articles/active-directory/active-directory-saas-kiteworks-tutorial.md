---
title: "Tutorial: integração do Azure Active Directory ao Kiteworks | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Kiteworks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3be35d6c7efea5e5a784ee3c0d1965cc11bfcfe
ms.openlocfilehash: f64add6d742de24d0144db44e7c3885feb7a1139
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>Tutorial: Integração do Active Directory do Azure com o Kiteworks
O objetivo desse tutorial é mostrar como integrar o Kiteworks ao Azure AD (Azure Active Directory). 

A integração do Kiteworks ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar no AD do Azure quem tem acesso ao Kiteworks 
* Você pode permitir que seus usuários façam logon automaticamente no Kiteworks usando SSO (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Active Directory do Azure 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Kiteworks, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Kiteworks com SSO habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Kiteworks da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="add-kiteworks-from-the-gallery"></a>Adicionar o Kiteworks da galeria
Para configurar a integração do Kiteworks ao AD do Azure, você precisará adicionar o Kiteworks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kiteworks da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Kiteworks**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)
7. No painel de resultados, selecione **Kiteworks** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Kiteworks com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Kiteworks é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kiteworks.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Kiteworks.

Para configurar e testar o SSO do Azure AD com o Kiteworks, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Kiteworks](#creating-a-kiteworks-test-user)** : para ter um equivalente de Brenda Fernandes no Kiteworks que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo Kiteworks. 

Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar o SSO para o Kiteworks, você precisa de um domínio registrado. Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Kiteworks.  

**Para configurar o logon único do AD do Azure com o Kiteworks, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Kiteworks**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Kiteworks**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) 
  1. Na caixa de texto **URL de Logon**, digite a URL utilizada pelos usuários para entrar no aplicativo Kiteworks (por exemplo: *https://fabrikam.kiteworks.com/*).
  2. Clique em **Próximo**.
4. Na página **Configurar logon único no Kiteworks** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png)   
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
  2. Clique em **Próximo**.
5. Faça logon no site da sua empresa do Kiteworks como um administrador.
6. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) 
7. Na seção **Autenticação e Autorização**, clique em **Configuração do SSO**. 
   
    ![Configurar Logon Único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) 
8. Na página Instalação do SSO, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png)   
  1. Selecione **Autenticar via SSO**.
  2. Selecione **Iniciar AuthnRequest**.
  3. No portal clássico do Azure, na página de diálogo **Configurar logon único no Kiteworks**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IDP**. 
  4. No portal clássico do Azure, na página do diálogo **Configurar logon único no Kiteworks**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL do Serviço de Logon Único**.
  5. No portal clássico do Azure, na página de diálogo **Configurar logon único no Kiteworks**, copie o valor da URL do **Serviço de Logoff Único** e cole-o na caixa de texto **URL do Serviço de Logoff Único**.
  6. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado de Chave Pública RSA** . 
  7. Clique em **Salvar**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
10. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png)  
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-kiteworks-test-user"></a>Criar um usuário de teste do Kiteworks
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Kiteworks.

O Kiteworks oferece suporte ao provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Kitewors se ele ainda não existir.

>[!NOTE]
>Se você precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Kiteworks.
>  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Kiteworks.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Kiteworks, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Kiteworks**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando você clica no bloco Kiteworks no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Kiteworks.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png







