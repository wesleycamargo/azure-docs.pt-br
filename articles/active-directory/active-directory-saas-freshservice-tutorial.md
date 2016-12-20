---
title: "Tutorial: integração do Azure Active Directory com o FreshService | Microsoft Docs"
description: "Saiba como usar o FreshService com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6fa6a1817bc01e6d9f6b10f7e6479cdc4f60ca2


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Tutorial: integração do Active Directory do Azure ao FreshService
O objetivo deste tutorial é mostrar a integração do Azure ao FreshService.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do FreshService com logon único habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao FreshService poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o FreshService
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")

## <a name="enabling-the-application-integration-for-freshservice"></a>Habilitando a integração de aplicativos com o FreshService
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o FreshService.

### <a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o FreshService, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **FreshService**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Application Gallery")
7. No painel de resultados, selecione **FreshService** e clique em **Concluir** para adicionar o aplicativo.
   
   ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no FreshService com a própria conta do Azure AD usando a federação baseada no protocolo SAML.  
A configuração do logon único para FreshService exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **FreshService**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no FreshService**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do FreshService**, digite a URL usada pelos usuários para entrar no aplicativo Freshdesk (por exemplo: "*http://democompany.freshservice.com/*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configure App URL")
4. Na página **Configurar logon único no FreshService**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do FreshService como administrador.
6. No menu na parte superior, clique em **Administrador**.
   
   ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
7. No **Portal do Cliente**, clique em **Segurança**.
   
   ![Segurança](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Security")
8. Na seção **Segurança** , realize as seguintes etapas:
   
   ![Logon Único](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")
   
   1. Ative o **Logon Único**.
   2. Selecione **SSO do SAML**.
   3. No portal clássico do Azure, na página de diálogo **Configurar logon único no FreshService**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do SAML**.
   4. No portal clássico do Azure, na página de diálogo **Configurar logon único no FreshService**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
   5. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado de Segurança**.
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no FreshService, eles devem ser provisionados no FreshService.  
No caso do FreshService, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **FreshService** como administrador.
2. No menu na parte superior, clique em **Administrador**.
   
   ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
3. Na seção **Gerenciamento de Usuários**, clique em **Solicitantes**.
   
   ![Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. Clique em **Novo Solicitante**.
   
   ![Novos Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. Na seção **Novo Solicitante** , realize as seguintes etapas:
   
   ![Novo Solicitante](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")
   
   1. Insira os atributos **Nome** e **Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Salvar**.
   
   > [!NOTE]
   > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa
   > 
   > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do FreshService ou APIs fornecidas pelo FreshService para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### <a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Para atribuir usuários ao FreshService, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **FreshService**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


