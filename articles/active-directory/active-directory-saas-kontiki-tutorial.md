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
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 230facec9f8f83e8a94eaaac5a1495195ff45cc7
ms.openlocfilehash: 3af125b186e3905572dd5d621c948a6639f9b023
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Active Directory do Azure com o Kontiki
O objetivo deste tutorial é mostrar a integração do Azure com o Kontiki.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura com SSO do Kontiki habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Kontiki poderão fazer logon único no aplicativo em seu site de empresa do Kontiki (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Kontiki
* Configurando o logon único
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Cenário")

## <a name="enable-the-application-integration-for-kontiki"></a>Habilitar a integração de aplicativos para o Kontiki
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Kontiki.

**Para habilitar a integração de aplicativos com o Kontiki, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Kontiki**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Kontiki**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Kontiki com a própria conta no AD do Azure usando federação baseada no protocolo SAML.

*Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **Kontiki**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Configurar Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurar Logon Único")
2. Na página **Como você gostaria que os usuários fizessem logon no Kontiki**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Kontiki**, digite a URL usada pelos usuários para fazer logon no Kontiki (por exemplo: "*https://company.mc.eval.kontiki.com/*"), em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Kontiki**, clique em **Baixar metadados**, em seguida, salve o arquivo de metadados em seu computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurar Logon Único")
5. Envie o metadatafile para a equipe de suporte do Kontiki.
   
   >[!NOTE]
   >A configuração de logon único deve ser executada pela equipe de suporte do Kontiki. Assim que a configuração for concluída, você receberá uma notificação. 
   > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Kontiki. Quando um usuário atribuído tenta fazer logon no Kontiki usando o painel de acesso, o Kontiki verifica se o usuário existe.  

>!NOTE] Se não houver conta de usuário ainda, ela será criada automaticamente pelo Kontiki.
>

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Kontiki, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. na página de integração do aplicativo **Kontiki**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


