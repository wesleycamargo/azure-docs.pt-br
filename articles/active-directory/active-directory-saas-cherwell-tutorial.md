---
title: "Tutorial: integração do Azure Active Directory ao Cherwell | Microsoft Docs"
description: "Saiba como usar o Cherwell com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 43acab94bfde65793c4ff944cb6db5dc5de47feb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Active Directory do Azure ao Cherwell
O objetivo deste tutorial é mostrar a integração do Azure ao Cherwell. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único (SSO) do Cherwell

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Cherwell poderão fazer logon único no aplicativo em seu site de empresa do Cherwell (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Cherwell
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Cenário")

## <a name="enable-the-application-integration-for-cherwell"></a>Habilitar a integração de aplicativos para o Cherwell
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cherwell.

**Para habilitar a integração de aplicativos para o Cherwell, execute as seguintes**
 etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Cherwell**.
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. No painel de resultados, escolha **Cherwell** e clique em **Concluir** para adicionar o aplicativo.
   
## <a name="configure-single-sign-on"></a>Configurar o logon único
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Cherwell com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

**Para configurar o SSO, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **Cherwell**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no Cherwell**, selecione **Logon único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar URL do Aplicativo")
  1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar em **Cherwell** (por exemplo: *https://\<nome da empresa\>.cherwellondemand.com/cherwellclient*). 
  2.  Clique em **Próximo**.
4. Na página **Configurar logon único no Cherwell** , realize as seguintes etapas:
   
   ![Configurar Logon Único](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar Logon Único")
  1.  Clique em **Baixar certificado**e salve o certificado localmente no computador.
  2.  Copie a **URL do Provedor de Identidade**.
  3.  Copie a **URL de Serviço de Logon Único**.
  4.  Clique em **Próximo**.
5. Envie o certificado de download, a **URL do Provedor de Identidade** e a **URL do Serviço de Logon Único** para a equipe de suporte do Cherwell.
   
   >[!NOTE]
   >A equipe de suporte do Cherwell precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.
   > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar Logon Único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para permitir que os usuários do AD do Azure façam logon no Cherwell, eles devem ser provisionados no Cherwell.

No caso do Cherwell, as contas de usuário precisam ser criadas pela equipe de suporte do Cherwell.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do Cherwell ou as APIs fornecidas pelo Cherwell para provisionar as contas de usuário do Active Directory do Azure.
>  

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

**Para atribuir usuários ao Cherwell, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Cherwell**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


