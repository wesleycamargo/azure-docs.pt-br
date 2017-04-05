---
title: "Tutorial: Integração do Azure Active Directory ao ThousandEyes | Microsoft Docs"
description: "Saiba como usar o ThousandEyes com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7bc96e6a711c70f9c5fa5daa4e059d9d7c04a134
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Active Directory do Azure ao ThousandEyes
O objetivo deste tutorial é mostrar como configurar o logon único entre o Azure AD (Active Directory do Azure) e o ThousandEyes.

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do ThousandEyes habilitada para SSO (logon único)

Depois de concluir este tutorial, os usuários do AAD aos quais você atribui acesso ao ThousandEyes poderão fazer o logon único no aplicativo no site da sua empresa ThousandEyes (serviço provedor iniciado pelo logon) ou usando o Introdução ao Painel de Acesso.

1. Habilitando a integração de aplicativos com o ThousandEyes
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Cenário")

## <a name="enable-the-application-integration-for-thousandeyes"></a>Habilitar a integração de aplicativos para o ThousandEyes
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ThousandEyes.

**Para habilitar a integração de aplicativos com o ThousandEyes, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **ThousandEyes**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **ThousandEyes** e clique em **Concluir** para adicionar o aplicativo.
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configure-single-sign-on"></a>Configurar o logon único
Esta descreve como permitir que os usuários se autentiquem no ThousandEyes com a respectiva conta do Active Directory do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativo do **ThousandEyes**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configurar Logon Único")

2. Na página **Como você deseja que os usuários façam logon no ThousandEyes**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configurar Logon Único")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do ThousandEyes**, digite a URL usada pelos usuários para entrar em seu aplicativo ThousandEyes (por exemplo: "*https://app.thousandeyes.com/login/sso*") e clique em **Avançar**. 
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no ThousandEyes**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configurar Logon Único")

5. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

6. No menu na parte superior, clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configurações")

7. Clique em **Conta**
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Conta")

8. Clique na guia **Segurança e Autenticação**.
   
    ![Segurança e autenticação](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Segurança e autenticação")

9. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Configurar o logon único")
  1. Selecione **Habilitar Logon Único**.
  2. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Página de Logon**.
  3. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Página de Logoff**.
  4. No Portal Clássico do Microsoft Azure, na página **Configurar logon único no ThousandEyes**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor do Provedor de Identidade**.
  5. Em **Certificado do Provedor de Identidade**, clique em **Escolher arquivo** e carregue o certificado que você baixou do Portal Clássico do Microsoft Azure.
  6. Clique em **Salvar**.

10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configurar Logon Único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para permitir que os usuários do AD do Azure façam logon no ThousandEyes, eles devem ser provisionados no ThousandEyes.  
No caso do ThousandEyes, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário no ThousandEyes, execute as seguintes etapas:**

1. Faça logon em seu site de empresa ThousandEyes como um administrador.

2. Clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configurações")

3. Clique em **Conta**.
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Conta")

4. Clique na guia **Contas e Usuários**.
   
    ![Contas e usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Contas e usuários")

5. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:
   
    ![Adicionar contas de usuário](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")   
  1. Digite o **Nome**, **Email** e outros detalhes de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
  2. Clique em **Adicionar Novo Usuário à Conta**.
      
     >[!NOTE]
     >O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.
     >  

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do AAD.
>  

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao ThousandEyes, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **ThousandEyes**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


