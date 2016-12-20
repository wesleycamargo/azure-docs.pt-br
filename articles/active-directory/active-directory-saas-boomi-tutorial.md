---
title: "Tutorial: Integração do Azure Active Directory ao Boomi | Microsoft Docs"
description: "Saiba como usar o Boomi com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração do Active Directory do Azure ao Boomi
O objetivo deste tutorial é mostrar a integração do Azure ao Boomi.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Boomi

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Boomi poderão fazer logon único no aplicativo em seu site de empresa do Boomi (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Boomi
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>Habilitando a integração de aplicativos para o Boomi
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Boomi.

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Boomi, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Boomi**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. No painel de resultados, selecione **Boomi** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Boomi com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Boomi**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Boomi**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Resposta do Boomi**, digite a **URL de Logon do Boomi AtomSphere** (por exemplo: “*https://platform.boomi.com/sso/AccountName/saml*”) e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. Na página **Configurar logon único no Boomi**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa Boomi como um administrador.
6. Na barra de ferramentas, na parte superior, clique no nome da sua empresa e em **Configuração**.
   
   ![Configuração](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. Clique em **Opções de SSO**.
   
   ![Opções de SSO](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. Na seção **Opções de Logon Único** , realize as seguintes etapas:
   
   ![Opções de Logon Único](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. Selecione **Habilitar Logon Único do SAML**.
   2. Clique em **Importar**para carregar o certificado baixado.
   3. No portal clássico do Azure, na página do diálogo **Configurar logon único no Boomi**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
   4. Para **Local do ID de Federação**, selecione **A ID de Federação está contida no elemento NameID da Entidade**.
   5. Clique em **Salvar**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Boomi, eles deverão ser provisionados no Boomi.  
No caso do Boomi, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Boomi** como administrador.
2. Vá para **Gerenciamento de Usuários \> Usuários**.
   
   ![Usuários](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. Clique em **Adicionar Usuário**.
   
   ![Adicionar Usuário](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. Na página do diálogo **Adicionar Funções de Usuário** , realize as seguintes etapas:
   
   ![Adicionar usuário](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. Nas caixas de texto correspondentes, digite o **Nome**, o **Sobrenome** e o **Email** de uma conta válida do AAD que você deseja provisionar.
   2. Clique em OK.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Boomi ou as APIs fornecidas pelo Boomi para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Para atribuir usuários ao Boomi, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Boomi**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


