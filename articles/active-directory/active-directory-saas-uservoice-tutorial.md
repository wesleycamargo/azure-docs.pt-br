---
title: "Tutorial: Integração do Azure Active Directory com o UserVoice | Microsoft Docs"
description: "Saiba como usar o UserVoice com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 592fc838645dbc05b710b1bd83fd7c0135688844
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Active Directory do Azure com o UserVoice
O objetivo deste tutorial é mostrar a integração do Azure com o UserVoice.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do UserVoice

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao UserVoice poderão fazer logon único no aplicativo em seu site de empresa do UserVoice (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o UserVoice
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Cenário")

## <a name="enabling-the-application-integration-for-uservoice"></a>Habilitando a integração de aplicativos para o UserVoice
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o UserVoice.

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o UserVoice, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **UserVoice**.
   
    ![Galeria de aplicativos](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galeria de aplicativos")

7. No painel de resultados, selecione **UserVoice** e clique em **Concluir** para adicionar o aplicativo.
   
    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir a autenticação dos usuários no UserVoice com sua conta do AD do Azure usando a federação baseada no protocolo SAML.  
Configurar o logon único para UserVoice exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativo do **UserVoice**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no UserVoice**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar logon único](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurar logon único")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do UserVoice**, digite a URL usando o padrão "*https://\<tenant-name\>.UserVoice.com*" e clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no UserVoice**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\UserVoice.cer**.
   
    ![Configurar logon único](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurar logon único")

5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa UserVoice como administrador.

6. Na barra de ferramentas na parte superior, clique em Configurações e, em seguida, selecione o portal da Web no menu.
   
    ![Configurações](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Configurações")

7. Na guia **Portal da Web**, na seção **Autenticação de usuário**, clique em **Editar** para abrir a página do diálogo **Editar Autenticação de Usuário**
   
    ![Portal da Web](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portal da Web")

8. Na página do diálogo **Editar Autenticação de Usuário** , realize as seguintes etapas:
   
    ![Editar autenticação de usuário](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Editar autenticação de usuário")
   
    a. Clique em **SSO (Logon Único)**.
 
    b. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no UserVoice**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **Entrada Remota de SSO**.

    c. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no UserVoice**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **Saída Remota de SSO**.
 
    d. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital do certificado SHA1 atual**.  
      
    > [!TIP]
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
  
    e. Clique em **Salvar Configurações de Autenticação**.

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar logon único](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurar logon único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no UserVoice, eles devem ser provisionados no UserVoice.  
No caso do UserVoice, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon no seu locatário **UserVoice** .

2. Vá para **Configurações**.
   
    ![Configurações](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Configurações")

3. Clique em **Geral**.

4. Clique em **Agentes e permissões**.
   
    ![Agentes e permissões](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agentes e permissões")

5. Clique em **Adicionar administradores**.
   
    ![Adicionar administradores](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Adicionar administradores")

6. No diálogo **Convidar administradores** , realize as seguintes etapas:
   
    ![Convidar administradores](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Convidar administradores")
   
    a. Na caixa de texto Emails, digite o endereço de email da conta que você deseja provisionar e clique em **Adicionar**.
   
    b. Clique em **Convidar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do UserVoice ou APIs fornecidas pelo UserVoice para provisionar contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Para atribuir usuários ao UserVoice, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **UserVoice**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Atribuir usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


