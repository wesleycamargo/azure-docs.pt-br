---
title: "Tutorial: integração do Azure Active Directory com o Zscaler Beta | Microsoft Docs"
description: "Saiba como usar o Zscaler Beta com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 54dd9ad56ac6cbb842597737a2c1841fac903c01


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Active Directory do Azure com o Zscaler Beta
O objetivo deste tutorial é mostrar a integração do Azure com o Zscaler Beta.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Zscaler Beta

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Zscaler Beta poderão fazer logon único no aplicativo em seu site de empresa do Zscaler Beta (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o ZScaler Beta
2. Configurando o logon único
3. Definindo as configurações de proxy
4. Configurando o provisionamento de usuários
5. Atribuindo usuários

![Cenário](./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler-beta"></a>Habilitando a integração de aplicativos com o ZScaler Beta
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ZScaler Beta.

### <a name="to-enable-the-application-integration-for-zscaler-beta-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o ZScaler Beta, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **ZScaler Beta**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "Application Gallery")
7. No painel de resultados, selecione **ZScaler Beta** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Zscaler One](./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "ZScaler One")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ZScaler Beta com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário carregar um certificado codificado em base 64 no locatário do ZScaler Beta.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **ZScaler Beta**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no ZScaler Beta**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Configure Single Sign-On")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do ZScaler Beta**, digite a URL usada pelos usuários para fazer logon em seu aplicativo ZScaler Beta e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "Configure App URL")
   
   > [!NOTE]
   > Você pode obter o valor real para o ambiente de sua equipe de suporte ZScaler Beta se necessário.
   > 
   > 
4. Na página **Configurar logon único no ZScaler Beta**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Configure Single Sign-On")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa ZScaler Beta como administrador.
6. No menu na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "Administration")
7. Em **Gerenciar Administradores e Funções**, clique em **Gerenciar Usuários e Autenticação**.
   
   ![Gerenciar Usuários e Autenticação](./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "Manage Users & Authentication")
8. Na seção **Escolher Opções de Autenticação para a sua Organização** , realize as seguintes etapas:
   
   ![Autenticação](./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "Authentication")
   
   1. Selecione **Autenticar usando o Logon Único do SAML**.
   2. Clique em **Configurar Parâmetros de Logon Único do SAML**.
9. Na página do diálogo **Configurar Parâmetros de Logon Único do SAML**, realize as seguintes etapas e clique em **Concluído**:
   
   ![Logon Único](./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "Single Sign-On")
   
   1. No portal clássico do Azure, na página do diálogo **Configurar logon único no ZScaler Beta**, copie o valor da **URL de Solicitação de Autenticação** e cole-o na caixa de texto **URL do Portal do SAML para o qual os usuários são enviados para autenticação**.
   2. Na caixa de texto **Atributo que contém o Nome de Logon**, digite **NameI**.
   3. Para carregar seu certificado baixado, clique em **Zscaler pem**.
   4. Selecione **Habilitar Provisionamento Automático do SAML**.
10. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:
    
    ![Administração](./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "Administration")
    
    1. Clique em **Salvar**.
    2. Clique em **Ativar Agora**.
11. No portal clássico do Azure, na página do diálogo **Configurar logon único no ZScaler Beta**, selecione a confirmação de configuração de logon único e clique em **Concluir**.
    
    ![Configurar o logon único](./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Configure Single Sign-On")

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer
1. Inicie o **Internet Explorer**.
2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir o diálogo **Opções da Internet**.
   
   ![Opções da Internet](./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "Internet Options")
3. Clique na guia **Conexões** .
   
   ![Conexões](./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "Connections")
4. Clique em **Configurações de LAN** para abrir o diálogo **Configurações de LAN**.
5. Na seção Servidor de proxy, execute as seguintes etapas:
   
   ![Servidor proxy](./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "Proxy server")
   
   1. Selecione Usar um servidor de proxy para a LAN.
   2. Na caixa de texto Endereço, digite **gateway.zscalerBeta.net**.
   3. Na caixa de texto Porta, digite **80**.
   4. Selecione **Ignorar servidor proxy para endereços locais**.
   5. Clique em **OK** para fechar o diálogo **Configurações de LAN (Rede de Área Local)**.
6. Clique em **OK** para fechar o diálogo **Opções da Internet**.

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no ZScaler Beta, eles devem ser provisionados no ZScaler Beta.  
No caso do ZScaler Beta, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça login no seu locatário do **Zscaler** .
2. Clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "Administration")
3. Clique em **Gerenciamento de Usuários**.
   
   ![Adicionar](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Add")
4. Na guia **Usuários**, clique em **Adicionar**.
   
   ![Adicionar](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Add")
5. Na seção Adicionar Usuário, execute as seguintes etapas:
   
   ![Adicionar usuário](./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "Add User")
   
   1. Digite **UserID**, **Nome de Exibição do Usuário**, **Senha** e **Confirmar Senha** e selecione **Grupos** e o **Departamento** de uma conta válida do AAD que você deseja provisionar.
   2. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ZScaler Beta ou APIs fornecidas pelo ZScaler Beta para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-zscaler-beta-perform-the-following-steps"></a>Para atribuir usuários ao ZScaler Beta, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **ZScaler Beta**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


