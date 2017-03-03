---
title: "Tutorial: Integração do Azure Active Directory om o InsideView | Microsoft Docs"
description: "Saiba como usar o InsideView com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 48a8cb0afd9fc2f9201169b074cf10f6d910952f
ms.openlocfilehash: 235b1f11c60a595dfc760701213cfc1b5f792b61
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Active Directory do Azure com o InsideView
O objetivo deste tutorial é mostrar a integração do Azure com o InsideView.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do InsideView

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao InsideView poderão fazer logon único no aplicativo em seu site de empresa do InsideView (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos com o InsideView
* Configurando o logon único
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-insideview-tutorial/IC794128.png "Cenário")

## <a name="enable-the-application-integration-for-insideview"></a>Habilitar a integração de aplicativos com o InsideView
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o InsideView.

**Para habilitar a integração de aplicativos com o InsideView, execute as seguintes etapas:**
 
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-insideview-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-insideview-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-insideview-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **InsideView**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-insideview-tutorial/IC794129.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **InsideView** e clique em **Concluir** para adicionar o aplicativo.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no InsideView com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
 
Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **InsideView**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no InsideView**, selecione **Logon Único do Microsoft Azure AD**e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Resposta do InsideView**, digite a URL de SSO do InsideView (por exemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no InsideView**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurar Logon Único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do InsideView como administrador.
6. Na barra de ferramentas na parte superior, clique em **Administrador**, **Configurações de Logon Único** e em **Adicionar SAML**.
   
   ![Configurações de Logon Único do SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "Configurações de Logon Único do SAML")
7. Na seção **Adicionar um Novo SAML** , realize as seguintes etapas:
   
   ![Adicionar um Novo SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Adicionar um Novo SAML")
   
   1. Na caixa de texto **Nome STS** , digite um nome para a sua configuração.
   2. No portal clássico do Azure, na página de diálogo **Configurar logon único no InsideView**, copie o valor do **Ponto de Extremidade Iniciado pelo Provedor de Serviços** e cole-o na caixa de texto **Ponto de Extremidade SamlP/WS-Fed Unsolicated**.
   3. Crie um arquivo **codificado em base&64;** usando o certificado baixado.      

     >[!TIP]
     >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
     >
   4. Abra seu certificado codificado base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado STS**
   5. Preencha o seguinte:
    * Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    * Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    * Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    * Na caixa de texto **Mapeamento de Id de Usuário do Crm**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Clique em **Salvar**.
8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do AD do Azure façam logon no InsideView, eles devem ser provisionados no InsideView. No caso do InsideView, o provisionamento é uma tarefa manual.

Para obter os usuários ou os contatos criados no InsideView, contate seu gerente de sucesso do cliente ou envie um email para **support@insideview.com**

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do InsideView ou APIs fornecidas pelo InsideView para provisionar as contas de usuário do AD do Azure.
>  

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao InsideView, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **InsideView**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-insideview-tutorial/IC794138.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-insideview-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


