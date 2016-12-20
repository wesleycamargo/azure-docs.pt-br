---
title: "Tutorial: Integração do Azure Active Directory ao Central Desktop | Microsoft Docs"
description: "Saiba como usar o Central Desktop com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5923bf1012c862d500d3c9b0062f0d7662e9f1bc


---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Active Directory do Azure ao Central Desktop
O objetivo deste tutorial é mostrar a integração do Azure ao Central Desktop. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Central Desktop/locatário do Central Desktop

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Central Desktop
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enabling-the-application-integration-for-central-desktop"></a>Habilitando a integração de aplicativos para o Central Desktop
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Central Desktop.

### <a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Central Desktop, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Central Desktop**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")
7. No painel de resultados, selecione **Central Desktop** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Central Desktop com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  
Como parte deste procedimento, é necessário carregar um certificado codificado em base 64 no locatário do Desktop Central.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Central Desktop**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Central Desktop**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**: 
   
   * Na caixa de texto **URL de Logon do Central Desktop**, digite a URL do seu locatário do Central Desktop (por exemplo: *http://contoso.centraldesktop.com*).
   * Na caixa de texto URL de Resposta do Central Desktop, digite a URL AssertionConsumerService do Central Desktop (por exemplo:  https://contoso.centraldesktop.com/saml2-assertion.php).
   
   > [!NOTE]
   > Você pode obter o valor nos metadados do Central Desktop (por exemplo: *http://contoso.centraldesktop.com*).
   > 
   > 
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")
4. Na página **Configurar logon único no Central Desktop**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")
5. Faça logon em seu locatário do **Central Desktop** .
6. Vá para **Configurações**, clique em **Avançadas** e em **Logon Único**.
   
   ![Configuração — Avançada](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. Na página **Configurações de Logon Único** , realize as seguintes etapas:
   
   ![Configurações de Logon Único](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
   1. Selecione **Habilitar Logon Único do SAML v2**.
   2. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL de SSO**.
   3. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de SSO**.
   4. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Serviço de Saída Única** e cole-o na caixa de texto **URL de Logout de SSO**.
8. Na seção **Método de Verificação de Assinatura de Mensagem** , realize as seguintes etapas:
   
   ![Método de Verificação de Assinatura de Mensagem](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
   1. Selecione **Certificado**.
   2. Na lista **Certificado de SSO**, selecione **RSH SHA256**.
   3. Crie um arquivo de texto por meio do certificado baixado, copie o conteúdo do arquivo de texto e cole-o no campo **Certificado de SSO** .  
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Selecione **Exibir um link para a sua página de logon do SAMLv2**.
9. Clique em **Atualizar**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Central Desktop. Esta seção descreve como criar contas de usuário do AAD no Central Desktop.

### <a name="to-provision-user-accounts-to-central-desktop"></a>Para provisionar contas de usuário no Central Desktop:
1. Faça logon no seu locatário do Central Desktop.
2. Vá para **Pessoas \> Membros Internos**.
3. Clique em **Adicionar Membros Internos**.
   
   ![Pessoas](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")
4. Na caixa de texto **Endereço de Email dos Novos Membros**, digite uma conta do AAD que você deseja provisionar e clique em **Avançar**.
   
   ![Endereços de Email dos Novos Membros](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. Clique em **Adicionar Membro(s) interno(s)**.
   
   ![Adicionar Membro Interno](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   > [!NOTE]
   > Os usuários que você adicionou receberão um email com um link de confirmação em que eles precisam clicar para ativar a conta.
   > 
   > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Central Desktop ou as APIs fornecidas pelo Central Desktop para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Para atribuir usuários ao Central Desktop, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Central Desktop**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


