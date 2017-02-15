---
title: "Tutorial: Integração do Azure Active Directory com o Pagerduty | Microsoft Docs"
description: "Saiba como usar o Pagerduty com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b473d7edb531f4fdab9c2ea8bde969e37558072


---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração do Active Directory do Azure com o Pagerduty
O objetivo deste tutorial é mostrar a integração do Azure com o Pagerduty.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Pagerduty

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Pagerduty poderão fazer logon único no aplicativo em seu site de empresa do Pagerduty (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Pagerduty
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>Habilitando a integração de aplicativos para o Pagerduty
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Pagerduty.

### <a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Pagerduty, execute as seguintes etapas:
1. No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Pagerduty**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")
7. No painel de resultados, selecione **Pagerduty** e clique em **Concluir** para adicionar o aplicativo.
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Pagerduty com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Pagerduty**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Pagerduty**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Pagerduty**, digite a URL usando o padrão "*https://\<nome-locatário\>.Pagerduty.com*" e clique em **Avançar**.
   
   ![Configurar a url do aplicativo](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")
4. Na página **Configurar logon único no Pagerduty**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa do Pagerduty como administrador.
6. No menu na parte superior, clique em **Configurações da Conta**.
   
   ![Configurações da Conta](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")
7. Clique em **logon único**.
   
   ![logon único](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")
8. Na página Habilitar Logon Único (SSO), execute as seguintes etapas:
   
   ![Habilitar logon único](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")
   
   1. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
      
      > [!TIP]
      > Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   2. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
   3. No portal clássico do Azure, na página de diálogo **Configurar logon único no Pagerduty**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   4. No portal clássico do Azure, na página de diálogo **Configurar logon único no Pagerduty**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
   5. Selecione **Ativar Logon Único**.
   6. Clique em **Salvar Alterações**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Pagerduty, eles devem ser provisionados no Pagerduty.  
No caso do Pagerduty, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **Pagerduty** .
2. No menu na parte superior, clique em **Usuários**.
3. Clique em **Adicionar Usuários**.
   
   ![Adicionar Usuários](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")
4. No diálogo **Convidar sua equipe**, digite o **Nome e Sobrenome** e o endereço de **Email** do usuário do Azure AD que você deseja provisionar, clique em **Adicionar** e em **Enviar Convites**.
   
   ![Convidar sua equipe](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")
   
   > [!NOTE]
   > Todos os usuários adicionados receberão um convite para criar uma conta do PagerDuty.
   > 
   > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Pagerduty ou as APIs fornecidas pelo Pagerduty para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Para atribuir usuários ao Pagerduty, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Pagerduty**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


