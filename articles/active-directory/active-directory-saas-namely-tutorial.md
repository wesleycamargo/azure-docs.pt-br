---
title: "Tutorial: integração do Azure Active Directory ao Namely | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Namely."
services: active-directory
documentationcenter: 
author: jeevansd
manager: prasannas
editor: 
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 88f03ad7c8f0e6c5f8e1d6341f0c25eadc6dda9d
ms.openlocfilehash: ec9bba280377f8ccf7c1561b75c0396dcf5142b6
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integração do Active Directory do Azure com o Namely
O objetivo deste tutorial é mostrar como integrar o Namely ao Azure AD (Azure Active Directory).

A integração do Namely ao Azure AD oferece os seguintes benefícios: 

* No AD do Azure, você pode controlar quem tem acesso ao Namely 
* Você pode permitir que seus usuários façam logon automaticamente no Namely usando SSO (logon único) com as contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Namely, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Namely habilitado para SSO

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Namely da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="add-namely-from-the-gallery"></a>Adicionar o Namely da galeria
Para configurar a integração do Namely ao AD do Azure, você precisa adicionar o do Namely da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o do Namely da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Namely**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)
7. No painel de resultados, selecione **Namely** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Namely, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Namely é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Namely.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Namely.

Para configurar e testar o SSO do Azure AD com o Namely, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do Namely](#creating-a-namely-test-user)** - para ter um equivalente de Brenda Fernandes no Namely que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo Namely. 

**Para configurar o SSO do Azure AD com o Namely, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Namely**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Namely**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) 
  1. Na caixa de texto **URL de Logon**, digite a URL utilizada pelos usuários para entrar no aplicativo Namely (por exemplo: *https://fabrikam.Namely.com/*).
  2. Clique em **Próximo**.
4. Na página **Configurar logon único no Namely** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) 
  1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
  2. Clique em **Próximo**.
5. Em outra janela do navegador, entre em seu site de empresa do Namely como administrador.
6. Na barra de ferramentas na parte superior, clique em **Empresa**.
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 
7. Clique na guia **Configurações** .
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 
8. Clique em **SAML**.
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 
9. Na página **Configurações de SAML** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) 
  1. Clique em **Habilitar SAML**. 
  2. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Namely**, copie o valor de **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de SSO do provedor de identidade**. 
  3. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado do provedor de identidade** .     
  4. Clique em **Salvar**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**. 
    
     ![Logon Único do AD do Azure][10]
11. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
     ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png)  
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-namely-test-user"></a>Criar um usuário de teste do Namely
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Namely.

**Para criar um usuário chamado Brenda Fernandes no Namely, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Namely como administrador.
2. Na barra de ferramentas na parte superior, clique em **Pessoas**.
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 
3. Clique na guia **Diretório** .
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 
4. Clique em **Adicionar Nova Pessoa**.
5. Na caixa de diálogo **Adicionar Nova Pessoa** , execute as seguintes etapas:
  1. Na caixa de texto **Nome**, digite **Brenda**.
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Email** , digite o endereço de email de Brenda Fernandes no Portal clássico do Azure.
  4. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Namely.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Namely, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Namely**.
   
    ![Configurar Logon Único](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Namely no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Namely.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png







