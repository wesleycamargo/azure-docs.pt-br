---
title: "Tutorial: Integração do Azure Active Directory ao ThousandEyes | Microsoft Docs"
description: "Saiba como usar o ThousandEyes com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: e0f85965cb884022f665d3664bc2b824095ad0fe


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Active Directory do Azure ao ThousandEyes
O objetivo deste tutorial é mostrar como configurar o logon único entre o Azure AD (Active Directory do Azure) e o ThousandEyes.

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do ThousandEyes habilitada para logon único

Depois de concluir este tutorial, os usuários do AAD aos quais você atribui acesso ao ThousandEyes poderão fazer o logon único no aplicativo no site da sua empresa ThousandEyes (serviço provedor iniciado pelo logon) ou usando o Introdução ao Painel de Acesso.

1. Habilitando a integração de aplicativos com o ThousandEyes
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

## <a name="enabling-the-application-integration-for-thousandeyes"></a>Habilitando a integração de aplicativos com o ThousandEyes
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ThousandEyes.

### <a name="to-enable-the-application-integration-for-thousandeyes-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o ThousandEyes, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **ThousandEyes**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")
7. No painel de resultados, selecione **ThousandEyes** e clique em **Concluir** para adicionar o aplicativo.
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
Esta descreve como permitir que os usuários se autentiquem no ThousandEyes com a respectiva conta do Active Directory do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativo do **ThousandEyes**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")

1. Na página **Como você deseja que os usuários façam logon no ThousandEyes**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar o Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do ThousandEyes**, digite a URL usada pelos usuários para entrar em seu aplicativo ThousandEyes (por exemplo: "*https://app.thousandeyes.com/login/sso*") e clique em **Avançar**. 
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4. Na página **Configurar logon único no ThousandEyes**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
    ![Configurar o Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")

5. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

6. No menu na parte superior, clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7. Clique em **Conta**
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

8. Clique na guia **Segurança e Autenticação**.
   
    ![Segurança e Autenticação](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")

9. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
    ![Configurações de Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")
   
    a. Selecione **Habilitar Logon Único**.
   
    b. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Página de Logon**.
   
    c. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Página de Logoff**.
   
    d. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor do Provedor de Identidade**.
   
    e. Em **Certificado do Provedor de Identidade**, clique em **Escolher arquivo** e carregue o certificado que você baixou do Portal Clássico do Microsoft Azure.
   
    f. Clique em **Salvar**.

10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no ThousandEyes, eles devem ser provisionados no ThousandEyes.  
No caso do ThousandEyes, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-to-thousandeyes-perform-the-following-steps"></a>Para provisionar uma conta de usuário no ThousandEyes, execute as seguintes etapas:
1. Faça logon em seu site de empresa ThousandEyes como um administrador.

2. Clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3. Clique em **Conta**.
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4. Clique na guia **Contas e Usuários**.
   
    ![Contas e Usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")

5. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:
   
    ![Adicionar contas de usuário](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")
   
    a. Digite o **Nome**, **Email** e outros detalhes de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Clique em **Adicionar Novo Usuário à Conta**.
      
    > [!NOTE]
    > O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.
    > 
    > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-thousandeyes-perform-the-following-steps"></a>Para atribuir usuários ao ThousandEyes, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **ThousandEyes**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


