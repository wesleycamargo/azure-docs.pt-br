---
title: "Tutorial: Integração do Azure Active Directory com o ITRP | Microsoft Docs"
description: "Saiba como usar o ITRP com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c4983024e5f5902620c0be7b422bc3d1eaf4a36f
ms.openlocfilehash: 8142454e1ee8bf439e9186ac94a3feb05b10ae69


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Active Directory do Azure com o ITRP
O objetivo deste tutorial é mostrar a integração do Azure com o ITRP.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do ITRP

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ITRP poderão fazer logon único no aplicativo em seu site de empresa do ITRP (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o ITRP
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario")

## <a name="enabling-the-application-integration-for-itrp"></a>Habilitando a integração de aplicativos para o ITRP
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ITRP.

### <a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o ITRP, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-itrp-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-itrp-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **ITRP**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-itrp-tutorial/IC775565.png "Application Gallery")

7. No painel de resultados, selecione **ITRP** e clique em **Concluir** para adicionar o aplicativo.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ITRP com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para ITRP exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativos do **ITRP**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configure single sign-on")

2. Na página **Como você deseja que os usuários façam logon no ITRP**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configure Single Sign-On")

3. Na página **Configurar URL do aplicativo**, na caixa de texto **URL de entrada do ITRP**, digite sua URL usando o seguinte padrão "*https://\<tenant-name\>.ITRP.com*" e, em seguida, clique em **Próximo**.
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configure App URL")

4. Na página **Configurar logon único no ITRP**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\ITRP.cer**.
   
    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configure Single Sign-On")

5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do ITRP como administrador.

6. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7. No painel de navegação à esquerda, selecione **Logon Único**.
   
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8. Na seção de configuração de logon único, execute as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")
    
    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")
   
    a. Clique em **Habilitar**.
   
    b. No portal clássico do Azure, na página do diálogo **Configurar logon único no ITRP**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff Remoto**.
   
    c. No portal clássico do Azure, na página do diálogo **Configurar logon único no ITRP**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de SSO do SAML**.
   
    d. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital do certificado**.
      
    > [!TIP]
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e. Clique em **Salvar**.

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no ITRP, eles devem ser provisionados no ITRP.  
No caso do ITRP, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **ITRP** .

2. Na barra de ferramentas na parte superior, clique em **Registros**.
   
    ![Administrador](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3. No menu pop-up, selecione **Pessoas**.
   
    ![Pessoas](./media/active-directory-saas-itrp-tutorial/IC775587.png "People")

4. Clique em **Adicionar Nova Pessoa** (“+”).
   
    ![Administrador](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5. Na caixa de diálogo Adicionar nova pessoa, execute as seguintes etapas:
   
    ![Usuário](./media/active-directory-saas-itrp-tutorial/IC775577.png "User")
   
    a. Digite o **Nome** e **Email** de uma conta válida do AAD que você deseja provisionar.
   
    b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ITRP ou APIs fornecidas pelo ITRP para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Para atribuir usuários ao ITRP, execute as seguintes etapas:
1. No portal do Azure AD, crie uma conta de teste.

2. Na página de integração de aplicativos do **ITRP**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-itrp-tutorial/IC775588.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-itrp-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


