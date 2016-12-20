---
title: "Tutorial: integração do Azure Active Directory com o e-Builder | Microsoft Docs"
description: "Saiba como usar o e-Builder com o Active Directory do Azure para habilitar logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d5068007-5a54-40ac-9cb8-2ceca89fd8ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c08989aa8dc966fc731be7a53c1eb0f1d57b5053


---
# <a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Tutorial: integração do Active Directory do Azure ao e-Builder
O objetivo deste tutorial é mostrar a integração do Azure ao e-Builder.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do e-Builder

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao e-Builder poderão fazer logon único no aplicativo em seu site de empresa do e-Builder (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o e-Builder
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenario")

## <a name="enabling-the-application-integration-for-e-builder"></a>Habilitando a integração de aplicativos para o e-Builder
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o e-Builder.

### <a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o e-Builder, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **e-Builder**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Application gallery")
7. No painel de resultados, selecione **e-Builder**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no e-Builder com a própria conta no Azure AD usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos **e-Builder**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Configurar o logon único](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configure single sign-on")
2. Na página **Como você gostaria que os usuários fizessem logon no e-Builder**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar o logon único](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do e-Builder**, digite sua URL usando o seguinte padrão "*https://\<nome-locatário\>.e-Builder.com*", em seguida, clique em **Próximo**.
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configure app URL")
4. Na página **Configurar logon único no e-Builder**, para baixar seus metadados, clique em **Baixar metadados**, em seguida, no arquivo de dados local, como **c:\\e-BuilderMetaData.xml**.
   
   ![Configurar o logon único](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configure single sign-on")
5. Encaminhe esse arquivo de metadados à equipe de suporte do e-Builder. A equipe de suporte precisa configurar o logon único para você.
6. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há qualquer item de ação para a configuração do provisionamento de usuário para o e-Builder.  
Quando um usuário atribuído tentar fazer logon no e-Builder usando o painel de acesso, o e-Builder verificará se o usuário existe.  
Se ainda não houver uma conta de usuário, ela será automaticamente criada pelo e-Builder.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### <a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Para atribuir usuários ao e-Builder, execute as etapas a seguir:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **e-Builder **, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


