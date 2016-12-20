---
title: "Tutorial: Integração do Azure Active Directory com o LogicMonitor | Microsoft Docs"
description: "Saiba como usar o LogicMonitor com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6dbb19605f92ff0c0d58478a56e564bab71123e4


---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração do Active Directory do Azure com o LogicMonitor
O objetivo deste tutorial é mostrar a integração do Azure com o LogicMonitor.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do LogicMonitor

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o LogicMonitor
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")

## <a name="enabling-the-application-integration-for-logicmonitor"></a>Habilitando a integração de aplicativos com o LogicMonitor
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o LogicMonitor.

### <a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o LogicMonitor, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **logicmonitor**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")
7. No painel de resultados, selecione **LogicMonitor** e clique em **Concluir** para adicionar o aplicativo.
   
   ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no LogicMonitor com sua conta do AD do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **LogicMonitor**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no LogicMonitor**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para fazer logon no LogicMonitor \(por exemplo: "*http://company.logicmonitor.com*"\) e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")
4. Na página **Configurar o logon único no LogicMonitor**, clique em **Baixar metadados** e salve-os no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")
5. Faça logon em seu site de empresa do **LogicMonitor** como administrador.
6. No menu na parte superior, clique em **Configurações**.
   
   ![Configurações](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")
7. Na guia de navegação à esquerda, clique em **Logon Único**
   
   ![Logon Único](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")
8. Na seção **Configurações de SSO (logon único)** , realize as seguintes etapas:
   
   ![Configurações de logon único](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")
   
   1. Selecione **Habilitar Logon Único**.
   2. Para **Atribuição de Função Padrão**, selecione **readonly**.
   3. Abra o arquivo de metadados baixado no bloco de notas e cole o conteúdo do arquivo na caixa de texto **Metadados do Provedor de Identidade** .
   4. Clique em **Salvar Alterações**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para usuários do AAD conseguirem efetuar logon, eles devem ser provisionados para o aplicativo LogicMonitor usando seus nomes de usuário do Active Directory do Azure.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon no site da sua empresa LogicMonitor como um administrador.
2. No menu na parte superior, clique em **Configurações** e em **Funções e Usuários**.
   
   ![Funções e Usuários](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")
3. Clique em **Adicionar**.
4. Na seção **Adicionar uma conta** , realize as seguintes etapas:
   
   ![Adicionar uma conta](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")
   
   1. Digite os valores para **Nome de usuário**, **Email**, **Senha** e D**igitar senha novamente** do usuário do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   2. Selecione **Funções**, **Exibir Permissões** e **Status**.
   3. Clique em **Enviar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do LogicMonitor ou APIs fornecidas pelo LogicMonitor para provisionar as contas de usuário do Active Directory do Azure.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Para atribuir usuários ao LogicMonitor, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **LogicMonitor**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


