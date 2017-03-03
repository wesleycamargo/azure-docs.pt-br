---
title: "Tutorial: integração do Azure Active Directory com o Greenhouse | Microsoft Docs"
description: "Saiba como usar o Greenhouse com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ad2e712cc1bcb7aea82b2c56a8a4a0f79e2ce1e0
ms.openlocfilehash: cce21156d962c7678e2a32c0953586b786c1ad0f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: integração do Active Directory do Azure ao Greenhouse
O objetivo deste tutorial é mostrar a integração do Azure ao Greenhouse.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura de SSO (logon único) do Greenhouse

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Greenhouse poderão fazer logon único no aplicativo em seu site de empresa do Greenhouse (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Greenhouse
* Configurando o SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Cenário")

## <a name="enable-the-application-integration-for-greenhouse"></a>Habilitar a integração de aplicativos para o Greenhouse
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Greenhouse.

**Para habilitar a integração de aplicativos para o Greenhouse, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **greenhouse**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Greenhouse**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Greenhouse com a própria conta do Azure AD usando a federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **Greenhouse**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurar logon único")
2. Na página **Como você gostaria que os usuários fizessem logon no Greenhouse**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar logon único](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon**, digite sua URL usando o seguinte padrão "*https://company.greenhouse.io*", em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Greenhouse**, clique em **Baixar metadados**, em seguida, salve o arquivo de metadados localmente no seu computador.
   
   ![Configurar logon único](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurar logon único")
5. Encaminhe esse arquivo de metadados à equipe de suporte do Greenhouse.

>[!NOTE]
>O logon único deve ser habilitado pela equipe de suporte do Greenhouse.
>

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar logon único](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do Azure AD façam logon no Greenhouse, eles devem ser provisionados no Greenhouse. No caso do Greenhouse, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Greenhouse** como administrador.
2. No menu na parte superior, clique em **Configurar**, em seguida, clique em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Usuários")
3. Clique em **Novos Usuários**.
   
   ![Novo Usuário](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Novo Usuário")
4. Na seção **Adicionar Novo Usuário** , realize as seguintes etapas:
   
   ![Adicionar Novo Usuário](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Adicionar Novo Usuário")
   1. Na caixa de texto **Inserir email de usuários** , digite o endereço de email de uma conta válida do Active Directory do Azure que você deseja provisionar.
   2. Clique em **Salvar**.    
   
      >[!NOTE]
      >O titular da conta do Active Directory do Azure recebe um email com um link para confirmar a conta antes que ela se torne ativa.
      >  

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Greenhouse ou as APIs fornecidas pelo Greenhouse para provisionar as contas de usuário do AAD. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Greenhouse, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Greenhouse**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


