---
title: "Tutorial: Integração do Azure Active Directory om o InsideView | Microsoft Docs"
description: "Saiba como usar o InsideView com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Active Directory do Azure com o InsideView
O objetivo deste tutorial é mostrar a integração do Azure com o InsideView.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do InsideView

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao InsideView poderão fazer logon único no aplicativo em seu site de empresa do InsideView (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o InsideView
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>Habilitando a integração de aplicativos com o InsideView
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o InsideView.

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o InsideView, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **InsideView**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. No painel de resultados, selecione **InsideView** e clique em **Concluir** para adicionar o aplicativo.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no InsideView com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **InsideView**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. Na página **Como você deseja que os usuários façam logon no InsideView**, selecione **Logon Único do Microsoft Azure AD**e clique em **Avançar**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Resposta do InsideView**, digite a URL de SSO do InsideView (por exemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. Na página **Configurar logon único no InsideView**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o Logon Único](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do InsideView como administrador.
6. Na barra de ferramentas na parte superior, clique em **Administrador**, **Configurações de Logon Único** e em **Adicionar SAML**.
   
   ![Configurações de logon único do SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. Na seção **Adicionar um Novo SAML** , realize as seguintes etapas:
   
   ![Adicionar um Novo SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. Na caixa de texto **Nome STS** , digite um nome para a sua configuração.
   2. No portal clássico do Azure, na página de diálogo **Configurar logon único no InsideView**, copie o valor do **Ponto de Extremidade Iniciado pelo Provedor de Serviços** e cole-o na caixa de texto **Ponto de Extremidade SamlP/WS-Fed Unsolicated**.
   3. Crie um arquivo **codificado em base 64** usando o certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado STS**
   5. Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   6. Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   7. Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Clique em **Salvar**.
8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no InsideView, eles devem ser provisionados no InsideView.  
No caso do InsideView, o provisionamento é uma tarefa manual.

Para obter os usuários ou os contatos criados no InsideView, contate seu gerente de sucesso do cliente ou envie um email para **support@insideview.com**

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do InsideView ou APIs fornecidas pelo InsideView para provisionar as contas de usuário do AD do Azure.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Para atribuir usuários ao InsideView, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **InsideView**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


