---
title: "Tutorial: Integração do Azure Active Directory com o Overdrive Books | Microsoft Docs"
description: "Saiba como usar o Overdrive Books com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 35c1dfb5aaee4427f40e72a2293c756ae4fcb76c


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Tutorial: Integração do Active Directory do Azure com o Overdrive Books
O objetivo deste tutorial é mostrar a integração do Azure com o OverDrive.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do OverDrive

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao OverDrive poderão fazer SSO (logon único) no aplicativo em seu site de empresa do OverDrive (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o OverDrive
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Cenário")

## <a name="enabling-the-application-integration-for-overdrive"></a>Habilitando a integração de aplicativos para o OverDrive
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o OverDrive.

**Para habilitar a integração de aplicativos para o OverDrive, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **OverDrive**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **OverDrive** e clique em **Concluir** para adicionar o aplicativo.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no OverDrive com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **OverDrive**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Habilitar logon único](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Habilitar logon único")
2. Na página **Como você deseja que os usuários façam logon no OverDrive**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurar logon único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do OverDrive**, digite a URL usando o padrão “*http://mslibrarytest.libraryreserve.com*” e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no OverDrive** , para baixar o arquivo de metadados e, em seguida, enviá-lo à equipe de suporte do OverDrive.
   
   ![Configurar logon único](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurar logon único")
   
   > [!NOTE]
   > A equipe de suporte do OverDrive configura o logon único para você e envia uma notificação quando a configuração é concluída.
   > 
   > 
5. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar logon único](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurar logon único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há nenhum item de ação para a configuração de provisionamento de usuário para o OverDrive.  
Quando um usuário atribuído tentar fazer logon no OverDrive, uma conta do OverDrive será automaticamente criada, se necessário.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do OverDrive ou as APIs fornecidas pelo OverDrive para provisionar as contas de usuário do AAD.
> 
 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao OverDrive, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **OverDrive**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Atribuir Usuários")
   
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


