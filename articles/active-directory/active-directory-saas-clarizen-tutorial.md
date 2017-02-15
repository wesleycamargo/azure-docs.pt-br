---
title: "Tutorial: Integração do Azure Active Directory ao Clarizen | Microsoft Docs"
description: "Saiba como usar o Clarizen com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: integração do Active Directory do Azure ao Clarizen
O objetivo deste tutorial é mostrar a integração do Azure ao Clarizen.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Clarizen

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Clarizen poderão fazer logon único no aplicativo em seu site de empresa do Clarizen (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Clarizen
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>Habilitando a integração de aplicativos para o Clarizen
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Clarizen.

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Clarizen, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Clarizen**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. No painel de resultados, selecione **Clarizen** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Clarizen com a própria conta no Azure AD usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Clarizen**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Clarizen**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. Na página **Configurar logon único no Clarizen**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. Em outra janela do navegador da Web, faça logon no site da empresa do **Clarizen** como administrador (por exemplo: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).
5. Clique no nome de usuário e em **Configurações**.
   
   ![Configurações](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. Clique na guia **Configurações Globais** e, ao lado de **Autenticação Federada**, clique em **editar**.
   
   ![Configurações Globais](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. No diálogo **Autenticação Federada** , realize as seguintes etapas:
   
   ![Autenticação Federada](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. Clique em **Carregar** para carregar o certificado baixado.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no Clarizen**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon**.
   3. No portal clássico do Azure, na página do diálogo **Configurar logout único no Clarizen**, copie o valor da **URL do Serviço de Logout Único** e cole-o na caixa de texto **URL de Logout**.
   4. Selecione **Usar POST**.
   5. Clique em **Salvar**.
8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Clarizen, eles deverão ser provisionados no Clarizen.  
No caso do Clarizen, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Clarizen** como administrador.
2. Clique em **Pessoas**.
   
   ![Pessoas](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. Clique em **Convidar Usuário**.
   
   ![Convidar Usuários](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. Na página Convidar Pessoas, execute as seguintes etapas:
   
   ![Convidar Pessoas](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. Na caixa de texto **Email** , digite o endereço de email de uma conta válida do Active Directory do Azure que você deseja provisionar.
   2. Clique em **Convidar**.
   
   > [!NOTE]
   > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.
   > 
   > 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Para atribuir usuários ao Clarizen, execute as etapas a seguir:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Clarizen**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


