---
title: 'Tutorial: Integração do Active Directory do Azure com o Zendesk | Microsoft Docs'
description: Saiba como usar o Zendesk com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Active Directory do Azure com o Zendesk
O objetivo deste tutorial é mostrar a integração do Azure com o Zendesk.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Zendesk

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Zendesk poderão fazer logon único no aplicativo em seu site de empresa do Zendesk (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração do aplicativo com o Zendesk
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

## <a name="enabling-the-application-integration-for-zendesk"></a>Habilitando a integração do aplicativo com o Zendesk
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Zendesk.

### <a name="to-enable-the-application-integration-for-zendesk,-perform-the-following-steps:"></a>Para habilitar a integração de aplicativos com o Zendesk, execute as seguintes etapas:
1. No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Zendesk**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")
7. No painel de resultados, selecione **Zendesk** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zendesk com a própria conta no AD do Azure usando federação baseada no protocolo SAML.  
A configuração do logon único para Zendesk exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal do Azure AD, na página de integração de aplicativos do **Zendesk**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Logon único](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Zendesk**, selecione **Logon único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
   
   a. Na caixa de texto **URL de Logon do Zendesk**, digite a URL usando o seguinte padrão: `https://<tenant-name>.zendesk.com`
   
   b. Clique em **Próximo**.
4. Na página **Configurar logon único no Zendesk**, clique em **Baixar certificado** e salve o arquivo de certificado localmente em seu computador.
   
   ![Configurar o logon único](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zendesk como administrador.
6. Clique em **Administrador**.
7. No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.
   
   ![Segurança](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")
8. Na página **Segurança**, clique na guia **Administrador e Agentes**.
9. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.
10. No portal do Azure AD, na página **Configurar logon único no Zendesk**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de SSO do SAML**.
11. No portal do Azure AD, na página **Configurar logon único no Zendesk**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff Remoto**.
    
    ![Logon único](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")
12. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital do certificado**.
    
    > [!TIP]
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
13. Clique em **Salvar**.
14. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
    
    ![Configurar o logon único](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do Azure AD façam logon no **Zendesk**, eles devem ser provisionados no **Zendesk**.  
No caso do **Zendesk**, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-to-zendesk,-perform-the-following-steps:"></a>Para provisionar uma conta de usuário no Zendesk, execute as seguintes etapas:
1. Faça logon em seu locatário do **Zendesk** .
2. Selecione a guia **Lista de Clientes** .
3. Selecione a guia **Usuário** e clique em **Adicionar**.
   
   ![Adicionar usuário](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")
4. Digite o endereço de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.
   
   ![Novo usuário](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-zendesk,-perform-the-following-steps:"></a>Para atribuir usuários ao Zendesk, execute as seguintes etapas:
1. No portal do Azure AD, crie uma conta de teste.
2. Na página de integração de aplicativos do **Zendesk**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


