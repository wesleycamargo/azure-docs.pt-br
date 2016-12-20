---
title: "Tutorial: Integração do Azure Active Directory com o Innotas | Microsoft Docs"
description: "Saiba como usar o Innotas com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: eb9e9c2c-4b09-4177-bbab-423fd657448e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d686f1ec3ec7a0ec5062e6da231ace8b47dbd83


---
# <a name="tutorial-azure-active-directory-integration-with-innotas"></a>Tutorial: Integração do Active Directory do Azure com o Innotas
O objetivo deste tutorial é mostrar a integração do Azure com o Innotas.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Innotas

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Innotas poderão fazer logon único no aplicativo em seu site de empresa do Innotas (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Innotas
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenario")

## <a name="enabling-the-application-integration-for-innotas"></a>Habilitando a integração de aplicativos para o Innotas
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Innotas.

### <a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Innotas, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-innotas-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-innotas-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Innotas**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-innotas-tutorial/IC777332.png "Application gallery")
7. No painel de resultados, selecione **Innotas** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Innotas com sua conta do AD do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Innotas**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Innotas**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de entrada do Innotas**, digite a URL usando o padrão "*https://\<tenant-name\>.Innotas.com*" e clique em **Avançar**.
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configure app URL")
4. Na página **Configurar logon único no Innotas**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de dados localmente como **c:\\InnotasMetaData.xml**.
   
   ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configure single sign-on")
5. Encaminhe esse arquivo de metadados à equipe de suporte do Innotas. A equipe de suporte precisa configurar o logon único para você.
6. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Innotas.  
Quando um usuário atribuído tenta fazer logon no Innotas usando o painel de acesso, o Innotas verifica se o usuário existe.  
Se não houver conta de usuário ainda, ela é criada automaticamente pelo Innotas.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Para atribuir usuários ao Innotas, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Innotas**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-innotas-tutorial/IC777339.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-innotas-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


