---
title: "Tutorial: Integração do Azure Active Directory com o Tinfoil Security | Microsoft Docs"
description: "Saiba como usar o Tinfoil Security com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: 11b14aee1ef28cd29976b138919ccea8b4763016


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração do Active Directory do Azure com o Tinfoil Security
O objetivo deste tutorial é mostrar a integração do Azure com o Tinfoil Security.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Tinfoil Security habilitada para logon único

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Tinfoil Security poderão fazer logon único no aplicativo em seu site de empresa do Tinfoil Security (logon iniciado pelo provedor de identidade) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Tinfoil Security
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Configurar o logon único](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>Habilitando a integração de aplicativos com o Tinfoil Security
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Tinfoil Security.

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Tinfoil Security, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **Tinfoil Security**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Application Gallery")

7. No painel de resultados, selecione **Tinfoil Security** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Tinfoil Security com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para o Tinfoil Security exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Tinfoil Security**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configure Single Sign-On")

2. Na página **Como você deseja que os usuários façam logon no Tinfoil Security**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configure Single Sign-On")

3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Resposta do Tinfoil Security**, digite a URL do ACS (Serviço do Consumidor de Declaração) do Tinfoil Security (por exemplo: "*https://www.tinfoilsecurity.com/saml/consume*") e clique em **Avançar**.
   
    > [!NOTE]
    > Você poderá obter a URL do ACS dos Metadados do Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).
    > 
    > 
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configure App URL")

4. Na página **Configurar logon único no Tinfoil Security**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Tinfoil Security.cer**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configure Single Sign-On")

5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Tinfoil Security como administrador.

6. Na barra de ferramentas na parte superior, clique em **Minha Conta**.
   
    ![Painel](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7. Clique em **Segurança**.
   
    ![Segurança](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Security")

8. Na página de configuração **Logon Único** , realize as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")
   
    a. Selecione **Habilitar SAML**.
   
    b. Clique em **Configuração Manual**.
   
    c. No portal clássico do Azure, na página do diálogo **Configurar logon único no Tinfoil Security**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL da Postagem do SAML**.
   
    d. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado do SAML**.  
      
    > [!TIP]
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e. Copie a **ID da sua Conta**.
   
    f. Clique em **Salvar**.

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configure Single Sign-On")

10. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributes")

11. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributes")
    
    a. Clique em **adicionar atributo de usuário**.

    b. Na caixa de texto **Nome do Atributo**, digite **accountid**.

    c. Na caixa de texto **Valor do atributo** , copie o valor da ID da conta que você tiver copiado na seção anterior.

    d. Clique em **Concluído**.

12. Clique em **Aplicar alterações**.

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no Tinfoil Security, eles devem ser provisionados no Tinfoil Security.  
No caso do Tinfoil Security, o provisionamento é uma tarefa manual.

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Para provisionar um usuário, execute as seguintes etapas:
1. Se o usuário fizer parte de uma conta Enterprise, você precisa entrar em contato com a equipe de suporte do Tinfoil Security para que a conta de usuário seja criada.
2. Se o usuário for um usuário de SaaS do Tinfoil Security regular, ele pode adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite para o email especificado para criar uma nova conta de usuário do Tinfoil Security.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Tinfoil Security ou APIs fornecidas pelo Tinfoil Security para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Para atribuir usuários ao Tinfoil Security, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Tinfoil Security**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


