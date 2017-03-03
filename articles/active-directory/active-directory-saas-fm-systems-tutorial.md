---
title: "Tutorial: integração do Azure Active Directory com o FM:Systems | Microsoft Docs"
description: "Saiba como usar o FM:Systems com o Azure Active Directory para habilitar o logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: e92f8cb6e980a0552b8ff836ed521e069ba811bb
ms.lasthandoff: 02/14/2017


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Tutorial: Integração do Azure Active Directory com o FM:Systems
O objetivo deste tutorial é mostrar a integração do Azure ao FM:Systems.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do FM:Systems

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao FM:Systems poderão fazer logon único no aplicativo em seu site de empresa do FM:Systems (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o FM:Systems
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Cenário")

## <a name="enabling-the-application-integration-for-fmsystems"></a>Habilitando a integração de aplicativos para o FM:Systems
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o FM:Systems.

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o FM:Systems, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **FM:Systems**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **FM:Systems** e clique em **Concluir** para adicionar o aplicativo.
   
    ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no FM:Systems com a própria conta do Azure AD usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **FM:Systems**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar Logon Único")

2. Na página **Como você deseja que os usuários façam logon no FM:Systems**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar Logon Único")

3. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar URL do Aplicativo")
   
    a. Na caixa de texto **URL de Logon do FM:Systems**, digite a sua **URL de Resposta** do FM:Systems (por exemplo: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  
      
    > [!NOTE]
    > Você pode obter esse valor com a equipe de suporte do FM:Systems.
    > 
    > 
   
    b. Clique em **Avançar**

4. Na página **Configurar logon único no FM:Systems**, para baixar os metadados, clique em **Baixar metadados** e salve os metadados no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar Logon Único")

5. Envie o arquivo de metadados baixado para a equipe de suporte do FM:Systems.
   
    > [!NOTE]
    > A equipe de suporte do FM:Systems precisa fazer a configuração real do SSO.
    > Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.
    > 
    > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar Logon Único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no FM:Systems, eles devem ser provisionados no FM:Systems.  
No caso do FM:Systems, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do FM:Systems como administrador.

2. Vá para **Administração do Sistema \> Gerenciar Segurança \> Usuários \> Lista de usuários**.
   
    ![Administração do Sistema](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administração do Sistema")

3. Clique em **Criar novo usuário**.
   
    ![Criar Novo Usuário](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Criar Novo Usuário")

4. Na seção **Criar Usuário** , realize as seguintes etapas:
   
    ![Criar usuário](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Criar usuário")
   
    a. Digite o nome de usuário, a senha e sua confirmação, o endereço de email e a ID de funcionário de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Clique em **Próximo**.
 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Para atribuir usuários ao  FM:Systems, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **FM:Systems**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


