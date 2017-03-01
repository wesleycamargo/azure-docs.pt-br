---
title: "Tutorial: Integração do Azure Active Directory ao Mindflash | Microsoft Docs"
description: "Saiba como usar o Mindflash com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 57a6428315a4942a0c6566fef5a4db4ee66cb55a
ms.openlocfilehash: 89bd515fa988e0347508b739dd0676c5eeb1f44d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Tutorial: Integração do Active Directory do Azure com o Mindflash
O objetivo deste tutorial é mostrar a integração do Azure com o Mindflash. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura com SSO (logon único) do Mindflash habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Mindflash poderão fazer logon único no aplicativo em seu site de empresa do Mindflash (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Mindflash
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Cenário")

## <a name="enabling-the-application-integration-for-mindflash"></a>Habilitando a integração de aplicativos para o Mindflash
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Mindflash.

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Mindflash, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Mindflash**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Mindflash** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Mindflash com sua conta do AD do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Mindflash**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no Mindflash**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon**, digite sua URL usando o padrão "*http://company.mindflash.com*" e, em seguida, clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurar URL do Aplicativo")
4. Na página **Configurar o logon único no Mindflash**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurar Logon Único")
5. Envie o metadatafile para a equipe de suporte do Mindflash.
   
   > [!NOTE]
   > A configuração de logon único deve ser executada pela equipe de suporte do Mindflash. Assim que a configuração for concluída, você receberá uma notificação.
   > 
   > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurar Logon Único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Mindflash, eles devem ser provisionados no Mindflash. No caso do Mindflash, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Mindflash** como administrador.
2. Vá para **Gerenciar Usuários**.
   
   ![Gerenciar Usuários](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gerenciar Usuários")
3. Clique em **Adicionar Usuários** e, em seguida, clique em **Novo**.
4. Na seção **Adicionar Novos Usuários** , realize as seguintes etapas:
   
   ![Adicionar Novos Usuários](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Adicionar Novos Usuários")
   
   1. Nas caixas de texto relacionadas, digite o **Nome**, o **Sobrenome** e o **Email** de uma conta válida do AAD que você deseja provisionar.
   2. Clique em **Adicionar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Mindflash ou as APIs fornecidas pelo Mindflash para provisionar as contas de usuário do AAD. 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Para atribuir usuários ao Mindflash, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Mindflash**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


