---
title: "Tutorial: Integração do Azure Active Directory ao AppDynamics | Microsoft Azure"
description: "Saiba como usar o AppDynamics com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração do Active Directory do Azure ao AppDynamics
O objetivo deste tutorial é mostrar a integração do Azure ao AppDynamics. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do AppDynamics

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao AppDynamics poderão fazer logon único no aplicativo em seu site de empresa do AppDynamics (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração do aplicativo para o AppDynamics
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>Habilitando a integração do aplicativo para o AppDynamics
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AppDynamics.

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o AppDynamics, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **AppDynamics**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. No painel de resultados, selecione **AppDynamics** e clique em **Concluir** para adicionar o aplicativo.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AppDynamics com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **AppDynamics**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. Na página **Como você deseja que os usuários entrem no AppDynamics**, selecione **Logon Único do Microsoft Azure AD** e clique em** Avançar**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do AppDynamics**, digite a sua URL usada pelos usuários para entrar no AppDynamics ("*https://companyname.saas.appdynamics.com*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. Na página **Configurar logon único no AppDynamics**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa AppDynamics como um administrador.
6. Na barra de ferramentas na parte superior, clique em **Configurações** e em **Administração**.
   
   ![Administração](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. Clique na guia **Provedor de Autenticação** .
   
   ![Provedor de Autenticação](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. Na seção **Provedor de Autenticação** , realize as seguintes etapas:
   
   ![Configuração de SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. Como **Provedor de Autenticação**, selecione **SAML**.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no AppDynamics**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   3. No portal clássico do Azure, na página do diálogo **Configurar logon único no AppDynamics**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
   4. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**
   6. Clique em **Salvar**.
      ![Salvar](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no AppDynamics, eles devem ser provisionados no AppDynamics.  
No caso do AppDynamics, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa AppDynamics como um administrador.
2. Vá para **Usuários**, e, em seguida, clique em **+** para abrir o diálogo **Criar Usuário**.
   
   ![Usuários](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. Na seção **Criar Usuário** , realize as seguintes etapas:
   
   ![Criar Usuário](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. Preencha os campos **Nome de usuário**, **Nome**, **Email**, **Nova Senha**, **Repetir Nova Senha** de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Salvar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do AppDynamics ou as APIs fornecidas pelo AppDynamics para provisionar as contas de usuário do AD do Azure.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Para atribuir usuários ao AppDynamics, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **AppDynamics**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


