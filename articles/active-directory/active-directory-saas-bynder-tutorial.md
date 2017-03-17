---
title: "Tutorial: Integração do Azure Active Directory ao Bynder | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Bynder."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4fb0ab26-b3b9-420a-8072-a0be80ea021e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: fc84c9d6c7648cb230db0d3cc10f31148a0a69a0
ms.openlocfilehash: 6786d7eb6a11405278ef7267f25279f9e39b3bde
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bynder"></a>Tutorial: Integração do Azure Active Directory ao Bynder
O objetivo desse tutorial é mostrar como integrar o Bynder com o Azure AD (Azure Active Directory).

A integração do Bynder ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Bynder
* Você pode permitir que seus usuários faça logon automaticamente no Bynder usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Bynder, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para SSO (logon único) do Bynder

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Microsoft Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Bynder da galeria
2. Configuração e testes do SSO do Microsoft Azure AD

## <a name="add-bynder-from-the-gallery"></a>Adicionar o Bynder da galeria
Para configurar a integração do Bynder ao Azure AD, você precisará adicionar o Bynder da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Bynder da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Bynder**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_01.png)
7. No painel de resultados, selecione **Bynder** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_001.png)

## <a name="configure-and-test-microsoft-azure-ad-sso"></a>Configurar e testar o SSO do Microsoft Azure AD
O objetivo desta seção é mostrar como configurar e testar o Logon Único (SSO) do Microsoft Azure AD com o Bynder, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Bynder é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bynder.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Bynder.

Para configurar e testar o SSO do Microsoft Azure AD com o Bynder, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar os usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o Logon Único do Microsoft Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Bynder](#creating-a-bynder-test-user)** - para ter um equivalente de Brenda Fernandes no Bynder que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o Logon Único do Microsoft Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-sso"></a>Configuração do SSO do Microsoft Azure AD
Nesta seção, você habilita o SSO do Microsoft Azure AD no portal clássico e configura o SSO no aplicativo Bynder.

**Para configurar o SSO do Microsoft Azure AD com o Bynder, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Bynder**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Bynder**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo no **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_04.png)
  1. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.getbynder.com/sso/SAML/authenticate/`
  2. Clique em **Próximo**.
4. Se quiser configurar o aplicativo no **modo iniciado pelo SP**, na página de diálogo **Definir Configurações do Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon** e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_10.png)
  1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.getbynder.com/login/`
  2. Clique em **Próximo**.
  
   >[!NOTE]
   >O valor para a URL de Entrada neste tutorial é apenas um espaço reservado. Para obter o valor real de seu ambiente, entre em contato com a Bynder.
   >

5. Na página **Configurar logon único no Bynder**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_05.png)  
  1. Clique em **Baixar metadados**e salve o arquivo no computador.
  2. Clique em **Próximo**.
6. Para que o SSO seja configurado para seu aplicativo, contate a equipe de suporte do Bynder. Anexe o arquivo de metadados baixado e compartilhe-o com a equipe do Bynder para configurar o SSO no lado dela.
7. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_06.png)
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bynder-tutorial/create_aaduser_08.png)
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="create-a-bynder-test-user"></a>Criar um usuário de teste do Bynder
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Bynder. O Bynder dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Bynder, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Bynder. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Bynder.

   ![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Bynder, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, escolha **Bynder**.
   
    ![Configurar Logon Único](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Microsoft Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Bynder no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo do Bynder.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_205.png

