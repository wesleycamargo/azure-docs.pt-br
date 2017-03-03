---
title: "Tutorial: Integração do Azure Active Directory ao Egnyte | Microsoft Docs"
description: "Saiba como usar o Egnyte com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 4e8857244038435430f3cabdf5f3eccad8a922bf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: integração do Active Directory do Azure ao Egnyte
O objetivo deste tutorial é mostrar a integração do Azure ao Egnyte.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Egnyte com SSO (logon único) habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Egnyte poderão fazer logon único no aplicativo em seu site de empresa do Egnyte (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Egnyte
* Configurando o logon único
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Cenário")

## <a name="enable-the-application-integration-for-egnyte"></a>Habilitar a integração de aplicativos para o Egnyte
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Egnyte.

**Para habilitar a integração de aplicativos para o Egnyte, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **egnyte**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Egnyte** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Egnyte com a própria conta do Azure AD usando a federação baseada no protocolo SAML.  

Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base-64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Egnyte**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários entrem no Egnyte**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Egnyte**, digite a URL usando o padrão "*https://company.answerhub.com*" e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Egnyte**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configurar Logon Único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa do Egnyte como administrador.
6. Clique em **Configurações**.
   
   ![Configurações](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Configurações")
7. No menu, clique em **Configurações**.
   
   ![Configurações](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Configurações")
8. Clique na guia **Configuração** e, em seguida, clique em **Segurança**.
   
   ![Segurança](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Segurança")
9. Na seção **Autenticação de Logon Único** , realize as seguintes etapas:
   
   ![Autenticação de Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Autenticação de Logon Único")   
   1. Para **Autenticação de logon único**, selecione **SAML 2.0**.
   2. Para **Provedor de identidade**, selecione **AzureAD**.
   3. No portal clássico do Azure, na página do diálogo **Configurar logon único no Egnyte**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de logon do provedor de identidade**.
   4. No portal clássico do Azure, na página do diálogo **Configurar logon único no Egnyte**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de entidade do provedor de identidade**.
   5. Crie um arquivo **codificado em base&64;** usando o certificado baixado.  
     >[!TIP]
     >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
   6. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade** .
   7. Para **Mapeamento de usuário padrão**, selecione **Endereço de email**.
   8. Para **Usar valor do emissor específico do domínio**, selecione **desabilitado**.
   9. Clique em **Salvar**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
   ![Configurar Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configurar Logon Único")
    
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do Azure AD façam logon no Egnyte, eles deverão ser provisionados no Egnyte. No caso do Egnyte, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Egnyte** como administrador.
2. Vá para **Configurações \> Usuários e Grupos**.
3. Clique em **Adicionar Novo Usuário**e selecione o tipo de usuário que deseja adicionar.
   
   ![Usuários](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Usuários")
4. Na seção **Novo Usuário Padrão** , realize as seguintes etapas:
   
   ![Novo Usuário Padrão](./media/active-directory-saas-egnyte-tutorial/IC787825.png "Novo Usuário Padrão")   
   1. Digite o **Email**, **Nome de usuário** e outros detalhes de uma conta válida do Azure Active Directory que você deseja provisionar.
   2. Clique em **Salvar**.
    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email de notificação.
    >

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Egnyte ou as APIs fornecidas pelo Egnyte para provisionar as contas de usuário do AAD.
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Egnyte, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Egnyte**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


