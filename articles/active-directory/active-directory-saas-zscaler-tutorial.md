---
title: "Tutorial: Integração do Azure Active Directory ao Zscaler | Microsoft Docs"
description: "Saiba como usar o ZScaler com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: 22f83af7fc19410df01350d40619e6520878bec7


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integração do Active Directory do Azure com o Zscaler
O objetivo deste tutorial é mostrar a integração do Azure com o Zscaler. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário no Zscaler

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Zscaler
2. Configurando o logon único
3. Definindo as configurações de proxy
4. Configurando o provisionamento de usuários
5. Atribuindo usuários

![Cenário](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Habilitando a integração de aplicativos com o Zscaler
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Zscaler.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Zscaler, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **Zscaler**.
   
    ![Galeria de aplicativos](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application gallery")

7. No painel de resultados, selecione **Zscaler** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zscaler com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário carregar um certificado para Zscaler.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal clássico do Azure, na página de integração de aplicativos do **Zscaler**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Habilitar logon único](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Enable single sign-on")

2. Na página **Como você deseja que os usuários entrem no Zscaler**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Zscaler**, digite a URL de entrada obtida do Zscaler e clique em **Avançar**: 
   
    > [!NOTE]
    > Entre em contato com a equipe de suporte do Zscaler se você não souber qual é a sua URL de entrada.
    > 
    > 
   
    ![Configurar a URL do aplicativo](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4. Na página **Configurar logon único no Zscaler** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")
   
    1. Clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Zscaler.cer**.
    2. Copie a **URL de solicitação de autenticação** na área de transferência.

5. Faça login no seu locatário do Zscaler.

6. No menu na parte superior, clique em **Administração**.
   
    ![Administração](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. Em **Gerenciar Administradores e Funções**, clique em **Gerenciar Usuários e Autenticação**.
   
    ![Gerenciar Administradores e Funções](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8. Na seção **Escolher Opções de Autenticação para a sua Organização** , realize as seguintes etapas:
   
    ![Escolha as Opções de Autenticação](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")
   
    1. Selecione **Autenticar usando o Logon Único do SAML**.
    2. Clique em **Configurar Parâmetros de Logon Único do SAML**.

9. Na página da caixa de diálogo **Configurar Parâmetros de Logon Único do SAML**, execute as seguintes etapas e clique em **Concluído**:
   
    ![Carregar um certificado](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")
   
    1. Na caixa de texto **URL do Portal do SAML para o qual os usuários são enviados para autenticação**, cole o valor do campo **URL de solicitação de autenticação** do Portal clássico do Azure.
   
    2. Na caixa de texto **Atributo que contém o Nome de Logon**, digite **NameID**.
   
    3. No campo **Carregar Certificado Público do SSL** , carregue o certificado que você baixou no portal clássico do Azure.
   
    4. Selecione **Habilitar Provisionamento Automático do SAML**.

10. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:
    
    ![Configurar Autenticação de Usuário](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")
    
    1. Clique em **Salvar**.
    2. Clique em **Ativar Agora**.

11. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")


## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.
   
    ![Opções da Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3. Clique na guia **Conexões** .
   
    ![Conexões](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:
   
    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")
   
    1. Selecione Usar um servidor de proxy para a LAN.

    2. Na caixa de texto endereço, digite **gateway.zscalertwo.net**.
   
    3. Na caixa de texto Porta, digite **80**.
   
    4. Selecione **Ignorar servidor proxy para endereços locais**.
   
    5. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)**.

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no ZScaler, eles devem ser provisionados no ZScaler.  
No caso do ZScaler, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça login no seu locatário do **Zscaler** .

2. Clique em **Administração**.

    ![Administração](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. Clique em **Gerenciamento de Usuários**.
   
    ![Gerenciamento de Usuários](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4. Na guia **Usuários**, clique em **Adicionar**.
   
    ![Adicionar](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5. Na seção Adicionar Usuário, execute as seguintes etapas:
   
    ![Adicionar usuário](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")
   
    1. Digite **UserID**, **Nome de Exibição do Usuário**, **Senha**, **Confirmar Senha** e selecione **Grupos** e o **Departamento** de uma conta válida do AAD que você deseja provisionar.
   
    2. Clique em **Salvar**.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Para atribuir usuários ao ZScaler, execute as seguintes etapas:

1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Zscaler**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


