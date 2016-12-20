---
title: "Tutorial: integração do Azure Active Directory com o BlueJeans | Microsoft Docs"
description: "Saiba como usar o BlueJeans com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 00f00d91e54d35fb9009a3e927dfc8833cd7571e


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>Tutorial: Integração do AD do Azure ao BlueJeans
O objetivo deste tutorial é mostrar a integração do Azure ao BlueJeans.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do BlueJeans

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao BlueJeans poderão fazer logon único no aplicativo em seu site de empresa do BlueJeans (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o BlueJeans
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")

## <a name="enabling-the-application-integration-for-bluejeans"></a>Habilitando a integração de aplicativos para o BlueJeans
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o BlueJeans.

### <a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o BlueJeans, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **BlueJeans**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")
7. No painel de resultados, selecione **BlueJeans** e clique em **Concluir** para adicionar o aplicativo.
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no BlueJeans com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **BlueJeans**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no BlueJeans**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do BlueJeans**, digite a URL usando o padrão "*https://company.BlueJeans.com*" e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")
4. Na página **Configurar logon único no BlueJeans**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa do **BlueJeans** como administrador.
6. Vá para **ADMINISTRADOR \> Configurações de Grupo \> Segurança**.
   
   ![Administrador](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")
7. Na seção **Segurança** , realize as seguintes etapas:
   
   ![Logon Único do SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")
   
   1. Selecione **Logon Único do SAML**.
   2. Selecione **Habilitar provisionamento automático**.
8. Siga em frente com as seguintes etapas:
   
   ![Caminho do Certificado](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
   
   1. Clique em **Escolher Arquivo**e carregue o certificado baixado.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no BlueJeans**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   3. No portal clássico do Azure, na página do diálogo **Configurar logon único no BlueJeans**, copie o valor da **URL de Alteração de Senha** e cole-o na caixa de texto **URL de Alteração de Senha**.
   4. No portal clássico do Azure, na página do diálogo **Configurar logon único no BlueJeans**, copie o valor da **URL de Logout Remoto** e cole-o na caixa de texto **URL de Logout**.
9. Siga em frente com as seguintes etapas:
   
   ![Salvar Alterações](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
   
   1. Na caixa de texto **ID do usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   2. Na caixa de texto **Email**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   3. Clique em **Salvar Alterações**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no BlueJeans, eles devem ser provisionados no BlueJeans.  
No caso do BlueJeans, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **BlueJeans** como administrador.
2. Vá para **ADMINISTRADOR \> Gerenciar Usuários \> Adicionar Usuário**.
   
   ![Administrador](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   > [!IMPORTANT]
   > A guia **Adicionar Usuário** só estará disponível se, na **guia Segurança**, a opção **Habilitar provisionamento automático** estiver desmarcada.
   > 
   > 
3. Na seção **Adicionar Usuário** , realize as seguintes etapas:
   
   ![Adicionar usuário](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
   
   1. Digite um **Nome de Usuário do BlueJeans**, **Endereço de email**, **ID de Reunião do BlueJeans**, **Senha de Moderador**, **Nome Completo** e a **Empresa** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Adicionar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans ou as APIs fornecidas pelo BlueJeans para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Para atribuir usuários ao BlueJeans, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **BlueJeans**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


