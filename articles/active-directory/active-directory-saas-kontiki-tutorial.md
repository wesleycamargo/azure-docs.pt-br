---
title: "Tutorial: Integração do Azure Active Directory com o Kontiki | Microsoft Docs"
description: "Saiba como usar o Kontiki com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 38b7f9772d5cf4e7b7861ef57804436de2a2f442


---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Active Directory do Azure com o Kontiki
O objetivo deste tutorial é mostrar a integração do Azure com o Kontiki.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Kontiki com logon único habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Kontiki poderão fazer logon único no aplicativo em seu site de empresa do Kontiki (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Kontiki
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")

## <a name="enabling-the-application-integration-for-kontiki"></a>Habilitando a integração de aplicativos para o Kontiki
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Kontiki.

### <a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Kontiki, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Kontiki**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Application Gallery")
7. No painel de resultados, selecione **Kontiki**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Kontiki com a própria conta no AD do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos **Kontiki**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Configurar o Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configure Single SignOn")
2. Na página **Como você gostaria que os usuários fizessem logon no Kontiki**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configure Single SignOn")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Kontiki**, digite a URL usada pelos usuários para fazer logon no Kontiki (por exemplo: "*https://company.mc.eval.kontiki.com/*"), em seguida, clique em **Próximo**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configure App URL")
4. Na página **Configurar logon único no Kontiki**, clique em **Baixar metadados**, em seguida, salve o arquivo de metadados em seu computador.
   
   ![Configurar o Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configure Single SignOn")
5. Envie o metadatafile para a equipe de suporte do Kontiki.
   
   > [!NOTE]
   > A configuração de logon único deve ser executada pela equipe de suporte do Kontiki. Assim que a configuração for concluída, você receberá uma notificação.
   > 
   > 
6. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Kontiki.  
Quando um usuário atribuído tenta fazer logon no Kontiki usando o painel de acesso, o Kontiki verifica se o usuário existe.  
Se não houver conta de usuário ainda, ela é criada automaticamente pelo Kontiki.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Para atribuir usuários ao Kontiki, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **Kontiki **, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


