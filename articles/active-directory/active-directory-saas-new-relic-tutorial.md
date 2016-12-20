---
title: "Tutorial: Integração do Azure Active Directory ao New Relic | Microsoft Docs"
description: "Saiba como usar o New Relic com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c7542454bab5e5c2fae88aeefa1a981ee165ff


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração do Active Directory do Azure com o New Relic
O objetivo deste tutorial é mostrar como configurar o logon único entre o Active Directory do Azure e o New Relic.

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do New Relic

Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu ao New Relic poderão fazer um logon único no aplicativo usando o Painel de Acesso do AAD.

1. Habilitando a integração de aplicativos para New Relic
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")

## <a name="enabling-the-application-integration-for-new-relic"></a>Habilitando a integração de aplicativos para New Relic
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o New Relic.

### <a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o New Relic, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **New Relic**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")
7. No painel de resultados, selecione **New Relic** e clique em **Concluir** para adicionar o aplicativo.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

Esta descreve como permitir que os usuários se autentiquem no New Relic com a respectiva conta do Active Directory do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **New Relic**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no New Relic**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do New Relic**, digite a URL usada pelos usuários para fazer logon em seu aplicativo New Relic e clique em **Avançar**. 
   
   A URL do aplicativo é a URL de locatário do New Relic (por exemplo: *https://rpm.newrelic.com*):
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")
4. Na página **Configurar logon único no New Relic**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")
5. Em outra janela do navegador da Web, entre em seu site de empresa do **New Relic** como administrador.
6. No menu na parte superior, clique em **Configurações de Conta**.
   
   ![Configurações da Conta](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")
7. Clique na guia **Segurança e autenticação** e na guia **Logon único**.
   
   ![Logon Único](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. Na página da caixa de diálogo SAML, execute as seguintes etapas:
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. Clique em **Escolher Arquivo** para carregar seu certificado baixado do Active Directory do Azure.
   2. No portal clássico do Azure, na página **Configurar logon único no New Relic**, copie o valor da **URL de Logon Remoto**e cole-o na caixa de texto **URL de logon remoto**.
   3. No portal clássico do Azure, na página **Configurar logon único no New Relic**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de aterrissagem de logoff**.
   4. Clique em **Salvar minhas alterações**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no New Relic, eles deverão ser provisionados no New Relic.  
No caso do New Relic, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Para provisionar uma conta de usuário no New Relic, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **New Relic** como administrador.
2. No menu na parte superior, clique em **Configurações de Conta**.
   
   ![Configurações da Conta](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")
3. No painel **Conta** no lado esquerdo, clique em **Resumo** e em **Adicionar usuário**.
   
   ![Configurações da Conta](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")
4. No diálogo **Usuários ativos** , realize as seguintes etapas:
   
   ![Usuários ativos](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")
   
   1. Na caixa de texto **Email** , digite o endereço de email de um usuário válido do Active Directory do Azure que você deseja provisionar.
   2. Como **Função**, selecione **Usuário**.
   3. Clique em **Adicionar este usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do New Relic ou as APIs fornecidas pelo New Relic para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Para atribuir usuários ao New Relic, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **New Relic**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


