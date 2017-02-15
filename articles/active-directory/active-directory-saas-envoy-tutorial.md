---
title: "Tutorial: Integração do Azure Active Directory com o Envoy | Microsoft Docs"
description: "Saiba como usar o Envoy com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cb3062d8b3ce6640ae07da8887f0e0f14a89d05


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Tutorial: integração do Active Directory do Azure ao Envoy
O objetivo deste tutorial é mostrar a integração do Azure ao Envoy.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Envoy

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Envoy poderão fazer logon único no aplicativo em seu site de empresa do Envoy (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Envoy
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")

## <a name="enabling-the-application-integration-for-envoy"></a>Habilitando a integração de aplicativos para o Envoy
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Envoy.

### <a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Envoy, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Envoy**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")
7. No painel de resultados, selecione **Envoy** e clique em** Concluir** para adicionar o aplicativo.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Envoy com a própria conta no Azure AD usando federação baseada no protocolo SAML.  
Configurar o logon único para o Envoy exige que você recupere um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Envoy**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Habilitar logon único](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Envoy**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Envoy**, digite sua URL usando o padrão "*https://\<nome-locatário\>.Envoy.com*" e clique em **Avançar**.
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")
4. Na página **Configurar logon único no Envoy**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Envoy.cer**.
   
   ![Configurar o logon único](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa do Envoy como administrador.
6. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. Clique em **Empresa**.
   
   ![Empresa](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. Clique em **SAML**.
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. Na seção de configuração da **Autenticação SAML** , realize as seguintes etapas:
   
   ![Autenticação SAML](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")
   
   > [!NOTE]
   > O valor para a ID de Local de Matriz é automaticamente gerada pelo aplicativo.
   > 
   > 
   
   1. Copie o valor de **Impressão digital** do certificado exportado e cole-o na caixa de texto **Impressão digital**.  
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   2. No portal clássico do Azure, na página de diálogo **Configurar logon único no Envoy**, copie o valor da **URL de SSO de SAML** e cole-o na caixa de texto **URL do SAML de HTTP do Provedor de Identidade**.
   3. Clique em **Salvar alterações**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há qualquer item de ação para a configuração do provisionamento de usuário para o Envoy.  
Quando um usuário atribuído tenta fazer logon no Envoy usando o painel de acesso, o Envoy verifica se o usuário existe.  
Se ainda não houver uma conta de usuário, ela será criada automaticamente pelo Envoy.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Para atribuir usuários ao Envoy, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Envoy**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


