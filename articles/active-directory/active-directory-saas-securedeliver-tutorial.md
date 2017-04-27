---
title: "Tutorial: Integração do Azure Active Directory com o Novatus | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SECURE DELIVER."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fccd5668-fe6f-4e6d-a9ce-ba4f321c33d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 72aaf3835604b1e26f1a3d79822c844be12f0627
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-secure-deliver"></a>Tutorial: integração do Azure Active Directory ao SECURE DELIVER
O objetivo deste tutorial é mostrar como integrar o SECURE DELIVER ao Azure AD (Azure Active Directory).  

A integração do SECURE DELIVER ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SECURE DELIVER
* Você pode permitir que seus usuários façam logon automaticamente no SECURE DELIVER (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao SECURE DELIVER, você precisa dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura do SECURE DELIVER com SSO (logon único) habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SECURE DELIVER pela galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-secure-deliver-from-the-gallery"></a>Adicionar o SECURE DELIVER por meio da galeria
Para configurar a integração do SECURE DELIVER ao Azure AD, você precisará adicionar o SECURE DELIVER da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SECURE DELIVER da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **SECURE DELIVER**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_01.png)

7. No painel de resultados, escolha **SECURE DELIVER** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Logotipo do aplicativo e o Nome na galeria](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o SECURE DELIVER, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do SECURE DELIVER é equivalente a um determinado usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SECURE DELIVER.  

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **Nome de usuário** no SECURE DELIVER.

Para configurar e testar o SSO do Azure AD com o SECURE DELIVER, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do SECURE DELIVER](#creating-a-secure-deliver-test-user)** : para ter um equivalente de Brenda Fernandes em Pessoas que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo SECURE DELIVER.

**Para configurar o logon único do Azure AD com o SECURE DELIVER, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **SECURE DELIVER**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no SECURE DELIVER**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_03.png) 

3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e, em seguida, clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_04.png) 
   1. Na caixa de texto **URL de Logon**, digite a URL utilizada pelos usuários para fazer logon em seu aplicativo SECURE DELIVER usando o seguinte padrão: **"https://i-securedeliver.jp/sd/\<nome da empresa\>/jsf/login/sso"**.
   2. Contate a Equipe de Suporte do SECURE DELIVER via [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) para obter a URL do locatário, caso não saiba o valor.
   3. Na caixa de texto **Identificador** , digite a URL do locatário. 
   4. Clique em **Próximo**.

4. Na página **Configurar logon único no SECURE DELIVER**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_05.png) 
   1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   2. Clique em **Próximo**.

5. Para que o SSO seja configurado para seu aplicativo, contate a equipe de suporte do SECURE DELIVER pelo email [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) e forneça o seguinte:
   
   * O arquivo de certificado baixado
   * A **ID de Entidade**
   * A **URL do Serviço de Logon Único**
   * A **URL do Serviço de Logoff Único**

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

###<a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste do SECURE DELIVER no Azure AD, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_06.png)  
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.  
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-secure-deliver-test-user"></a>Criar um usuário de teste do SECURE DELIVER
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SECURE DELIVER. Trabalhe com a equipe de suporte do SECURE DELIVER para adicionar os usuários à conta do SECURE DELIVER.

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do SECURE DELIVER.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao SECURE DELIVER.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SECURE DELIVER, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **SECURE DELIVER**.
   
    ![Configurar Logon Único](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Ao clicar no bloco do SECURE DELIVER no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do SECURE DELIVER.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_205.png

