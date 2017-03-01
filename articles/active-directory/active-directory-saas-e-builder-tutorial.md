---
title: "Tutorial: integração do Azure Active Directory com o e-Builder | Microsoft Docs"
description: "Saiba como usar o e-Builder com o Active Directory do Azure para habilitar logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d5068007-5a54-40ac-9cb8-2ceca89fd8ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 2a0db2eda8f33d6a0637541015eb07446cfc0123
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Tutorial: integração do Active Directory do Azure ao e-Builder
O objetivo deste tutorial é mostrar a integração do Azure ao e-Builder.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do e-Builder

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao e-Builder poderão fazer logon único no aplicativo em seu site de empresa do e-Builder (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o e-Builder
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Cenário")

## <a name="enable-the-application-integration-for-e-builder"></a>Habilitar a integração de aplicativos para o e-Builder
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o e-Builder.

**Para habilitar a integração de aplicativos para o e-Builder, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **e-Builder**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galeria de aplicativos")
7. No painel de resultados, selecione **e-Builder**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
   ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no e-Builder com a própria conta no Azure AD usando federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **e-Builder**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.
   
   ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurar logon único")
2. Na página **Como você gostaria que os usuários fizessem logon no e-Builder**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
   ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do e-Builder**, digite sua URL usando o seguinte padrão "*https://\<nome-locatário\>.e-Builder.com*", em seguida, clique em **Próximo**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no e-Builder**, para baixar seus metadados, clique em **Baixar metadados**, em seguida, no arquivo de dados local, como **c:\\e-BuilderMetaData.xml**.
   
   ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurar logon único")
5. Encaminhe esse arquivo de metadados à equipe de suporte do e-Builder. A equipe de suporte precisa configurar o logon único para você.
6. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Não há qualquer item de ação para a configuração do provisionamento de usuário para o e-Builder.  
Quando um usuário atribuído tentar fazer logon no e-Builder usando o painel de acesso, o e-Builder verificará se o usuário existe.  

* Se ainda não houver uma conta de usuário, ela será automaticamente criada pelo e-Builder.

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao e-Builder, execute as etapas a seguir:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **e-Builder **, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


