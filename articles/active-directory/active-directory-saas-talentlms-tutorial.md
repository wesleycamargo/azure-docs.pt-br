---
title: "Tutorial: integração do Azure Active Directory com o TalentLMS | Microsoft Docs"
description: "Saiba como usar o TalentLMS com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8c03fddb8cabedb9a877cd57ff9aa7c47211016d


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração do Active Directory do Azure ao TalentLMS
O objetivo deste tutorial é mostrar a integração do Azure ao TalentLMS.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do TalentLMS

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TalentLMS poderão fazer logon único no aplicativo em seu site de empresa do TalentLMS (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o TalentLMS
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

## <a name="enabling-the-application-integration-for-talentlms"></a>Habilitando a integração de aplicativos para o TalentLMS
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TalentLMS.

### <a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o TalentLMS, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **TalentLMS**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Application gallery")
7. No painel de resultados, selecione **TalentLMS** e clique em **Concluir** para adicionar o aplicativo.
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TalentLMS com sua conta do Azure AD usando federação baseada em protocolo SAML. .  
Configurar o logon único para o TalentLMS exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativo do **TalentLMS**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no TalentLMS**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do TalentLMS**, digite sua URL usando o padrão “*https://\<tenant-name\>.TalentLMSapp.com*” e clique em **Avançar**.
   
   ![URL de logon](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Sign on URL")
4. Na página **Configurar logon único no TalentLMS**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c\\:\TalentLMS.cer**.
   
   ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa TalentLMS como um administrador.
6. Na seção **Conta e Configurações**, clique na guia **Usuários**.
   
   ![Conta e Configurações](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account & Settings")
7. Clique em **SSO (Logon Único)**.
8. Na seção de Configurações de Logon Único, execute as seguintes etapas:
   
   ![Logon Único](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")
   
   1. Na lista **Tipo de integração de SSO**, selecione **SAML 2.0**.
   2. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no TalentLMS**, copie o valor da **ID de Provedor de Identidade** e cole-o na caixa de texto **IdP (Provedor de Identidade)**.
   3. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital do certificado**.
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no TalentLMS**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de entrada remota**.
   5. No Portal Clássico do Azure, na página de caixa de diálogo **Configurar logon único no TalentLMS**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **URL de saída remota**.
   6. Na caixa de texto **TargetedID**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
   7. Na caixa de texto **Nome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
   8. Na caixa de texto **Sobrenome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
   9. Na caixa de texto **Email**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
   10. Clique em **Salvar**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no TalentLMS, eles devem ser provisionados no TalentLMS.  
No caso do TalentLMS, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **TalentLMS** .
2. Clique em **Usuários** e em **Adicionar Usuário**.
3. Na página do diálogo **Adicionar usuário** , realize as seguintes etapas:
   
   ![Adicionar usuário](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Add User")
   
   1. Digite os valores dos atributos relacionados da conta de usuário do Azure AD nas seguintes caixas de texto: **Nome**, **Sobrenome** e **Endereço de email**.
   2. Clique em **Adicionar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TalentLMS ou as APIs fornecidas pelo TalentLMS para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Para atribuir usuários ao TalentLMS, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **TalentLMS**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


