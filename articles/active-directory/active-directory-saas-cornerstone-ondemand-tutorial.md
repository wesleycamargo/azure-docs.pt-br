---
title: "Tutorial: integração do Azure Active Directory com o Cornerstone OnDemand | Microsoft Docs"
description: "Saiba como usar o Cornerstone OnDemand com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bbbae7d7a5e9b0fbfe07bd3f2aa61df77b56c5df
ms.openlocfilehash: 7fce07464b90c81da7a015246d5757ecc768792a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Tutorial: integração do Active Directory do Azure ao Cornerstone OnDemand
O objetivo deste tutorial é mostrar a integração do Azure ao Cornerstone OnDemand.

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Cornerstone OnDemand

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Cornerstone OnDemand poderão fazer logon único no aplicativo em seu site de empresa do Cornerstone OnDemand (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Cornerstone OnDemand
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Cenário")

## <a name="enable-the-application-integration-for-cornerstone-ondemand"></a>Habilitar a integração de aplicativos para o Cornerstone OnDemand
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cornerstone OnDemand.

**Para habilitar a integração de aplicativos para o Cornerstone OnDemand, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **cornerstone ondemand**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Cornerstone OnDemand**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Cornerstone OnDemand com a própria conta no Azure AD usando federação baseada no protocolo SAML.

**Para configurar o SSO, execute as seguintes etapas:**
1. No portal clássico do Azure, na página de integração de aplicativos **Cornerstone OnDemand**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Habilitar logon único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Habilitar logon único")
2. Na página **Como você gostaria que os usuários fizessem logon no Cornerstone OnDemand**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Logon Único do AD do Microsoft Azure")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Cornerstone OnDemand**, digite sua URL usando o seguinte padrão "*http://company.csod.com*", em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Cornerstone OnDemand**, para baixar seu certificado, clique em **Baixar certificado**, em seguida, salve o arquivo de certificado localmente no seu computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar Logon Único")
5. Envie os itens a seguir para a equipe de suporte do Cornerstone OnDemand:  
   1. O certificado baixado
   2. O valor da **URL de Logon Remoto**
   3. O valor da **URL de Logoff Remoto** .

   >[!NOTE]
   >O Logon Único precisa ser configurado pela equipe de suporte do Cornerstone OnDemand. Assim que a configuração for concluída, você receberá uma notificação da equipe de suporte.
   > 
6. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do Azure AD façam logon no Cornerstone OnDemand, eles devem ser provisionados no Cornerstone OnDemand. No caso do Cornerstone OnDemand, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Envie as informações (por exemplo: Nome, Email) sobre o usuário do Azure AD que você deseja provisionar à equipe de suporte do Cornerstone OnDemand.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Cornerstone OnDemand ou APIs fornecidas pelo Cornerstone OnDemand para provisionar as contas de usuário do AAD. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Cornerstone OnDemand, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **Cornerstone OnDemand **, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Atribuir Usuários](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Atribuir Usuários")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


