---
title: "Tutorial: Integração do Azure Active Directory com o Syncplicity | Microsoft Docs"
description: "Saiba como usar o Syncplicity com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 57c649e974cc0514d3a534a5977d537bdd2bb7c5
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Active Directory do Azure com o Syncplicity
O objetivo deste tutorial é mostrar como configurar o logon único entre o Azure AD (Active Directory do Azure) e o Syncplicity.

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Syncplicity

Depois de concluir este tutorial, os usuários do Azure AD aos quais você atribuiu acesso ao Syncplicity poderão fazer logon único no aplicativo em seu site de empresa do Syncplicity (logon iniciado pelo provedor de serviços) ou usando o Painel de Acesso do Azure AD.

1. Habilitando a integração de aplicativos para o Syncplicity
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Cenário")

## <a name="enabling-the-application-integration-for-syncplicity"></a>Habilitando a integração de aplicativos para o Syncplicity
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Syncplicity.

### <a name="to-enable-the-application-integration-for-syncplicity-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Syncplicity, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **Syncplicity**.
   
    ![Galeria de aplicativos do Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Galeria de aplicativos do Syncplicity")

7. No painel de resultados, selecione **Syncplicity** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
Esta descreve como permitir que os usuários se autentiquem no Syncplicity com a respectiva conta do Active Directory do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Syncplicity**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no Syncplicity**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Logon Único do Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Logon Único do Microsoft Azure AD")

3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Syncplicity**, digite a URL usada pelos usuários para entrar no aplicativo Syncplicity e clique em **Avançar**. 
   
    A URL do aplicativo é a URL de locatário do Syncplicity (por ex.: *http://company.Syncplicity.com*):
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no Syncplicity**, para baixar o certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
    ![Configurar logon único](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configurar logon único")

5. Entre no locatário do **Syncplicity** .

6. No menu, na parte superior, clique em **administrador**, selecione **configurações** e clique em **Domínio personalizado e logon único**.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7. Na página do diálogo **SSO (Logon Único)** , realize as seguintes etapas:
   
    ![Logon Único \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")
   
    a. Na caixa de texto **Domínio Personalizado** , digite o nome do seu domínio.
   
    b. Selecione **Habilitado** como **Status do Logon Único**.
   
    c. No portal clássico do Azure, na página **Configurar logon único no Syncplicity**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade**.
   
    d. No portal clássico do Azure, na página **Configurar logon único no Syncplicity**, copie o valor da **URL de Serviço de Logon Único** e cole-o na caixa de texto **URL da página de conexão**.
   
    e. No portal clássico do Azure, na página **Configurar logon único no Syncplicity**, copie o valor da **URL do Logoff Remoto** e cole-o na caixa de texto **URL da página de logoff**.
   
    f. Em **Certificado do Provedor de Identidade**, clique em **Escolher arquivo** e carregue o certificado que você baixou no portal clássico do Azure.
   
    g. Clique em **Salvar Alterações**.

8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Confirmação](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmação")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do AAD no Syncplicity.

### <a name="to-provision-a-user-account-to-syncplicity-perform-the-following-steps"></a>Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:
1. Faça logon em seu locatário do **Syncplicity** (por ex.: *https://company.Syncplicity.com*).

2. Clique em **Administrador** e selecione **contas de usuário**.

3. Clique em **Adicionar um usuário**.
   
    ![Gerenciar Usuários](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Gerenciar Usuários")

4. Digite o **Endereço de email** da conta do AAD que você deseja provisionar, selecione **Usuário** como **Função** e clique em **Avançar**.
   
    ![Dados da Conta](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Dados da Conta")
   
    > [!NOTE]
    > O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.
    > 
    > 

5. Selecione um grupo em sua empresa do qual o novo usuário deverá se tornar membro e clique em **Avançar**.
   
    ![Associação a um Grupo](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Associação a um Grupo")
   
    > [!NOTE]
    > Se não houver nenhum grupo listado, basta clicar em **Avançar**.
    > 
    > 

6. Selecione as pastas que você gostaria de colocar sob o controle do Syncplicity no computador do usuário e clique em **Avançar**.
   
    ![Pastas do Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Pastas do Syncplicity")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Syncplicity ou as APIs fornecidas pelo Syncplicity para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-syncplicity-perform-the-following-steps"></a>Para atribuir usuários ao Syncplicity, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Syncplicity**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Atribuir usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


