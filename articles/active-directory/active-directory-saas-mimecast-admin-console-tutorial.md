---
title: "Tutorial: Integração do Azure Active Directory com o Mimecast Admin Console | Microsoft Docs"
description: "Saiba como usar o Mimecast Admin Console com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7271ace1c6bd1d56e20106e09ba125ac59145d1d


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Tutorial: Integração do Active Directory do Azure com o Mimecast Admin Console
O objetivo deste tutorial é mostrar a integração do Azure com o Mimecast Admin Console.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Mimecast Admin Console com logon único habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Mimecast Admin Console poderão fazer logon único no aplicativo em seu site de empresa do Mimecast Admin Console (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Mimecast Admin Console
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Habilitando a integração de aplicativos com o Mimecast Admin Console
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Mimecast Admin Console.

### <a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Mimecast Admin Console, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Mimecast Admin Console**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Application Gallery")
7. No painel de resultados, selecione **Mimecast Admin Console** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Mimecast Admin Console com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Mimecast Admin Console**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Mimecast Admin Console**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configure Single Sign-On")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do Mimecast Admin Console**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Mimecast Admin Console (por exemplo, “https://webmail-uk.mimecast.com” ou “https://webmail-us.mimecast.com”) e clique em **Avançar**.
   
   > [!NOTE]
   > A URL de logon é específica para a região.
   > 
   > 
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configure App URL")
4. Na página **Configurar logon único no Mimecast Admin Console**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configure Single Sign-On")
5. Em outra janela do navegador da Web, faça logon em seu Mimecast Admin Console como um administrador.
6. Vá para **Serviços \> Aplicativo**.
   
   ![Serviços](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")
7. Clique em **Perfis de Autenticação**.
   
   ![Perfis de Autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles")
8. Clique em **Novo Perfil de Autenticação**.
   
   ![Nenhum perfil de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profiles")
9. Na seção **Perfil de Autenticação** , realize as seguintes etapas:
   
   ![Perfil de Autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile")
   
   1. Na caixa de texto **Descrição** , digite um nome para a sua configuração.
   2. Selecione **Impor Autenticação SAML para o Mimecast Admin Console**.
   3. Como **Provedor**, selecione **Azure Active Directory**.
   4. No portal clássico do Azure, na página do diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL do Emissor**.
   5. No portal clássico do Azure, na página de diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   6. No portal clássico do Azure, na página de diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logoff**.  
      
      > [!NOTE]
      > O valor da URL de logon e da URL de logoff para o Mimecast Admin Console são as mesmas.
      > 
      > 
   7. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   8. Abra seu certificado codificado em base 64 no bloco de notas, remova a primeira linha ("*--*") e a última linha ("*--*"), copie o conteúdo restante para a área de transferência e cole-o na caixa de texto **Certificado de Provedor de Identidade (Metadados)**.
   9. Selecione **Permitir Logon Único**.
   10. Clique em **Salvar**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Mimecast Admin Console, eles devem ser provisionados no Mimecast Admin Console.  
No caso do Mimecast Admin Console, o provisionamento é uma tarefa manual.

Você precisa registrar um domínio antes de criar usuários.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon no **Mimecast Admin Console** como administrador.
2. Vá para **Diretórios \> Interno**.
   
   ![Diretórios](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories")
3. Clique em **Registrar Novo Domínio**.
   
   ![Registrar Novo Domínio](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain")
4. Depois de criar o novo domínio, clique em **Novo Endereço**.
   
   ![Novo Endereço](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address")
5. Na caixa de diálogo do novo endereço, execute as seguintes etapas:
   
   ![Salvar](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")
   
   1. Digite os atributos **Endereço de Email**, **Nome Global**, **Senha** e **Confirmar Senha** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Mimecast Admin Console ou as APIs fornecidas pelo Mimecast Admin Console para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Para atribuir usuários ao Mimecast Admin Console, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Mimecast Admin Console**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


