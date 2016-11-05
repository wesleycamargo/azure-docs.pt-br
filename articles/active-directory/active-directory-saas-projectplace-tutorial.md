---
title: 'Tutorial: Integração do Active Directory do Azure com o Projectplace | Microsoft Docs'
description: Saiba como usar o Projectplace com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-projectplace"></a>Tutorial: Integração do Active Directory do Azure ao Projectplace
O objetivo deste tutorial é mostrar a integração do Azure com o Projectplace.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Projectplace com logon único habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Projectplace poderão fazer logon único no aplicativo em seu site de empresa do Projectplace (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Projectplace
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")

## <a name="enabling-the-application-integration-for-projectplace"></a>Habilitando a integração de aplicativos com o Projectplace
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Projectplace.

### <a name="to-enable-the-application-integration-for-projectplace,-perform-the-following-steps:"></a>Para habilitar a integração de aplicativos para o Projectplace, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Projectplace**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")
7. No painel de resultados, selecione **Projectplace** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Projectplace com sua conta do AD do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal clássico do Azure, na página de integração de aplicativos do **Projectplace**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")
2. Na página **Como você deseja que os usuários façam logon no Projectplace**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Projectplace**, digite a URL de locatário do Projectplace (por exemplo: "*http://company.projectplace.com*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")
4. Na página **Configurar o logon único no Projectplace**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
   ![Configurar o Logon Único](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")
5. Envie o metadatafile para a equipe de suporte do Projectplace.
   
   > [!NOTE]
   > A configuração de logon único deve ser executada pela equipe de suporte do Projectplace. Assim que a configuração for concluída, você receberá uma notificação.
   > 
   > 
6. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Projectplace, eles devem ser provisionados no Projectplace.  
No caso do Projectplace, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Projectplace** como administrador.
2. Vá para **Pessoas** e clique em **Membros**.
   
   ![Pessoas](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")
3. Clique em **Adicionar Membro**.
   
   ![Adicionar Membros](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")
4. Na seção **Adicionar Membro** , realize as seguintes etapas:
   
   ![Novos Membros](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")
   
   1. Na caixa de texto **Novos Membros** , digite o endereço de email de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Enviar**
      
      > [!NOTE]
      > Um email incluindo um link para confirmar a conta antes que ela se torne ativa é enviado ao titular da conta do Active Directory do Azure.
      > 
      > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Projectplace ou APIs fornecidas pelo Projectplace para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-projectplace,-perform-the-following-steps:"></a>Para atribuir usuários ao Projectplace, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Projectplace**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


