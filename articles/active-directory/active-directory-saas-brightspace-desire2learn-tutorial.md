---
title: "Tutorial: Integração do Azure Active Directory ao Brightspace by Desire2Learn | Microsoft Docs"
description: "Saiba como usar o Brightspace by Desire2Learn com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integração do Active Directory do Azure ao Brightspace by Desire2Learn
O objetivo deste tutorial é mostrar a integração do Azure ao Brightspace by Desire2Learn.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para SSO (logon único) do Brightspace by Desire2Learn

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Brightspace by Desire2Learn poderão fazer logon único no aplicativo em seu site de empresa do Brightspace by Desire2Learn (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos ao Brightspace by Desire2Learn
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Cenário")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>Habilitar a integração de aplicativos ao Brightspace by Desire2Learn
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Brightspace by Desire2Learn.

**Para habilitar a integração de aplicativos para o Brightspace by Desire2Learn, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Brightspace by Desire2Learn**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Brightspace by Desire2Learn** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Brightspace by Desire2Learn com sua conta do AD do Azure usando federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Brightspace by Desire2Learn**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários entrem no Brightspace by Desire2Learn**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurar URL do Aplicativo")   
   1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no seu **Brightspace by Desire2Learn** (por exemplo: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
   2. Clique em **Próximo**.
4. Na página **Configurar logon único no Brightspace by Desire2Learn**, para baixar seus metadados, clique em **Baixar metadados** e salve os metadados no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurar Logon Único")
5. Envie o arquivo de metadados baixado para a equipe de suporte do Brightspace by Desire2Learn.
   
   >[!NOTE]
   >A equipe de suporte do Brightspace by Desire2Learn precisa fazer a configuração real do SSO.
   >Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.
   > 
 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do AD do Azure façam logon no Brightspace by Desire2Learn, eles devem ser provisionados no Brightspace by Desire2Learn.  

No caso do Brightspace by Desire2Learn, as contas de usuário precisam ser criadas pela equipe de suporte do Brightspace by Desire2Learn.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do Brightspace by Desire2Learn ou as APIs fornecidas pelo Brightspace by Desire2Learn para provisionar as contas de usuário do Active Directory do Azure. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Brightspace por Desire2Learn, execute as seguintes etapas:**
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Brightspace by Desire2Learn**, clique em **Atribuir usuários**.
   
  ![Atribuir Usuários](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
  ![Sim](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


