---
title: "Tutorial: integração do Azure Active Directory com o Directions on Microsoft | Microsoft Docs"
description: "Saiba como usar o Directions on Microsoft o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: efea82512dad3f2e5d1c0dd45e9e2f6db9a283d4
ms.openlocfilehash: a5538e89b1c23e4a2ad45c5515de8167ef95b46a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Tutorial: integração do Active Directory do Azure ao Directions on Microsoft
O objetivo deste tutorial é mostrar a integração do Active Directory do Azure ao Directions on Microsoft.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Directions on Microsoft

Se ainda não tem uma assinatura federada do Directions on Microsoft, envie uma solicitação por email para **service@DirectionsOnMicrosoft.com**.

Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu ao Directions on Microsoft poderão fazer um logon único no aplicativo usando o logon único.

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Directions on Microsoft
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Cenário")

## <a name="enable-the-application-integration-for-directions-on-microsoft"></a>Habilitar a integração de aplicativos para o Directions on Microsoft
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Directions on Microsoft.

**Para habilitar a integração de aplicativos para o Directions on Microsoft, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Directions on Microsoft**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Directions on Microsoft**e clique em **Concluir** para adicionar o aplicativo.
   
   ![Cenário](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Cenário")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Directions on Microsoft com sua própria conta do Azure AD usando a federação baseada no protocolo SAML.

**Para configurar o SSO, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos **Directions on Microsoft**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Habilitar logon único](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Habilitar logon único")
2. Na página **Como você gostaria que os usuários fizessem logon no Directions on Microsoft**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Logon Único do Microsoft Azure AD](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Logon Único do Microsoft Azure AD")
3. Na página **Configurar URL do aplicativo**, na caixa de texto URL de Logon, digite **https://www.directionsonmicrosoft.com/user/login**, em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Directions on Microsoft**, clique em **Baixar metadados**, em seguida, salve o arquivo de metadados localmente no seu computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar Logon Único")
5. Envie o arquivo de metadados à equipe de suporte do Directions on Microsoft (*service@DirectionsOnMicrosoft.com*). Para permitir que a equipe de suporte do Directions on Microsoft localize sua associação federado no site, inclua informações da sua empresa em seu email.
   
   >[!NOTE]
   >O Logon único para o Directions on Microsoft precisa ser habilitado pela equipe de suporte do Directions on Microsoft. Você receberá uma notificação quando o logon único for habilitado. 
   > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Não há qualquer item de ação para a configuração do provisionamento de usuário para o Directions on Microsoft.  

Quando um usuário atribuído tenta fazer logon no Directions on Microsoft usando o painel de acesso, o Directions on Microsoft verifica se o usuário existe. Se ainda não houver uma conta de usuário disponível, ela será automaticamente criada pelo Directions on Microsoft.

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Directions on Microsoft, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **Directions on Microsoft**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sim")


