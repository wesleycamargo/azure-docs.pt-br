---
title: "Tutorial: Integração do Azure Active Directory com Abintegro | Microsoft Docs"
description: "Saiba como usar o Abintegro com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 99287e1f-4189-494a-97c8-e1c03d047fd3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ddbe54eb6f9565e0c42d138231dbde22592e75b5
ms.openlocfilehash: d6191e01450859a5e91b815df84d9a024bbee2c3
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Tutorial: Integração do Active Directory do Azure ao Abintegro
O objetivo deste tutorial é mostrar a integração do Azure ao Abintegro.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para SSO (logon único) do Abintegro

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Abintegro poderão fazer logon único no aplicativo em seu site de empresa do Abintegro (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração do aplicativo para o Abintegro
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Cenário")

## <a name="enable-the-application-integration-for-abintegro"></a>Habilitar a integração de aplicativo para o Abintegro
O objetivo desta seção é descrever como habilitar a integração de aplicativos para Abintegro.

**Para habilitar a integração de aplicativos para Abintegro, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-abintegro-tutorial/IC749322.png "adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **abintegro**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Abintegro**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Abintegro com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos **Abintegro**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Configurar Logon Único](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurar Logon Único")
2. Na página **Como você gostaria que os usuários fizessem logon no Abintegro**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar Logon Único](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurar Logon Único")
3. Na página **Configurar URL do aplicativo**, na caixa de texto **URL de Logon do Abintegro**, digite a URL usada pelos usuários para fazer logon no Abintegro (por exemplo: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Abintegro**, clique em **Baixar metadados**, em seguida, salve o arquivo de metadados em seu computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurar Logon Único")
5. Envie o arquivo de metadados para a equipe de suporte do Abintegro.
   
   >[!NOTE]
   >A configuração de logon único deve ser executada pela equipe de suporte do Abintegro. Assim que a configuração for concluída, você receberá uma notificação.
   >  
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Abintegro.  
Quando um usuário atribuído tenta fazer logon no Abintegro usando o painel de acesso, o Abintegro verifica se o usuário existe.  

Se ainda não houver conta de usuário disponível, ela será criada automaticamente pelo Abintegro.

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Abintegro, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **Abintegro **, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


