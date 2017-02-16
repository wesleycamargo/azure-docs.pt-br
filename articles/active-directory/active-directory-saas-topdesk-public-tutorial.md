---
title: "Tutorial: integração do Azure Active Directory com o TOPdesk - Público | Microsoft Docs"
description: "Saiba como usar o TOPdesk - Público com o Azure Active Directory para habilitar logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: ee413f5044d541a7a127690fab0115341c7619f3


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Diretório do Azure ao TOPdesk - Público
O objetivo deste tutorial é mostrar a integração do Azure com o TOPdesk - Público.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do TOPdesk - Public

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TOPdesk - Public poderão fazer logon único no aplicativo em seu site de empresa do TOPdesk - Public (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o TOPdesk - Público
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>Habilitando a integração de aplicativos com o TOPdesk - Público
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TOPdesk - Público.

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o TOPdesk - Público, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **TOPdesk - Public**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Application Gallery")

7. No painel de resultados, selecione **TOPdesk - Public** e clique em **Concluir** para adicionar o aplicativo.
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TOPdesk - Público com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para o TOPdesk - Público requer que você carregue um arquivo de ícone de logotipo. Para obter o arquivo do ícone, entre em contato com a equipe de suporte do TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2. No menu **TOPdesk**, clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

3. Clique em **Configurações de Logon**.
   
    ![Configurações de Logon](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4. Expanda o menu **Configurações de Logon** e clique em **Geral**.
   
    ![Geral](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5. Na seção **Público** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:
   
    ![Configurações Técnicas](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
    a. Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.
   
    b. Abra o arquivo de metadados e localize o nó **AssertionConsumerService** .
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. Copie o valor de **AssertionConsumerService** .  
      
    > [!NOTE]
    > Você precisará do valor na seção **Configurar URL do Aplicativo** mais adiante neste tutorial.
    > 
    > 

6. Em outra janela do navegador da Web, faça logon no **portal clássico do Azure** como administrador.

7. Na página de integração de aplicativos do **TOPdesk - Public**, clique em **Configurar logon único**, para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configure Single Sign-On")

8. Na página **Como você deseja que os usuários façam logon no TOPdesk - Public**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configure Single Sign-On")

9. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configure App URL")
   
    a. Na caixa de texto **URL de Logon do TOPdesk - Public**, digite a URL usada pelos usuários para entrar no aplicativo TOPdesk - Public (por exemplo, "*https://qssolutions.topdesk.net*").
   
    b. Na caixa de texto **TOPdesk – URL de Resposta Pública**, cole **TOPdesk - URL AssertionConsumerService Pública** (por exemplo, "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   
    c. Clique em **Próximo**.

10. Na página **Configurar logon único no TOPdesk - Public**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo localmente no computador.
    
    ![Configurar o logon único](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configure Single Sign-On")

11. Execute as seguintes etapas para criar um arquivo de certificado:
    
    ![Certificado](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. Abra o arquivo de metadados baixado.
    2. Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.
    3. Copie o valor do nó **X509Certificate** .
    4. Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.
12. Em seu site de empresa do TOPdesk - Public, no menu **TOPdesk**, clique em **Configurações**.
    
    ![Configurações](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
13. Clique em **Configurações de Logon**.
    
    ![Configurações de Logon](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. Expanda o menu **Configurações de Logon** e clique em **Geral**.
    
    ![Geral](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. Na seção **Público**, clique em **Adicionar**.
    
    ![Logon do SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")
16. Na página do diálogo **Assistente de configuração do SAML** , realize as seguintes etapas:
    
    ![Assistente de configuração do SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    a. Para carregar o arquivo de metadados baixado, em **Metadados de Federação**, clique em **Procurar**.

    b. Para carregar o arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.

    d. Na caixa de texto **Atributo de nome de usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Na caixa de texto **Nome de exibição** , digite um nome para a sua configuração.

    f. Clique em **Salvar**.

17. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Público, eles deverão ser provisionados no TOPdesk - Público.  
No caso do TOPdesk - Público, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> Novo \> Arquivos de Suporte \> Pessoa**.
   
    ![Pessoa](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3. Na caixa de diálogo Nova Pessoa, execute as seguintes etapas:
   
    ![Nova Pessoa](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
    a. Clique na guia Geral.

    b. Na caixa de texto Sobrenome, digite o sobrenome referente a uma conta válida do Active Directory do Azure que você deseja provisionar.
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk - Público ou APIs fornecidas pelo TOPdesk - Público para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Para atribuir usuários ao TOPdesk - Público, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos **TOPdesk - Public**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


