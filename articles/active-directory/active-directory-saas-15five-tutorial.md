---
title: "Tutorial: Integração do Azure Active Directory ao 15Five | Microsoft Docs"
description: "Saiba como usar o 15Five com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>Tutorial: Integração do Active Directory do Azure ao 15Five
O objetivo deste tutorial é mostrar a integração do Azure ao 15Five. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do 15Five

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao 15Five poderão fazer logon único no aplicativo em seu site de empresa do 15Five (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o 15Five
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-15five-tutorial/IC784667.png "Cenário")

## <a name="enabling-the-application-integration-for-15five"></a>Habilitando a integração de aplicativos para o 15Five
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o 15Five.

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o 15Five, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-15five-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-15five-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **15Five**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-15five-tutorial/IC784668.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **15Five** e clique em **Concluir** para adicionar o aplicativo.
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no 15Five com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **15Five**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no 15Five**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do 15Five**, digite a URL usando o padrão "*https://company.15Five.com*" e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no 15Five**, clique em **Baixar metadados** e encaminhe o arquivo de metadados para a equipe de suporte do 15Five.
   
   ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar logon único")
   
   > [!NOTE]
   > O logon único precisa ser habilitado pela equipe de suporte do 15Five.
   > 
   > 
5. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar logon único")
   

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no 15Five, eles devem ser provisionados no 15Five.  
No caso do 15Five, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **15Five** como administrador.
2. Vá para **Gerenciar Empresa**.
   
   ![Gerenciar Empresa](./media/active-directory-saas-15five-tutorial/IC784675.png "Gerenciar Empresa")
3. Vá para **Pessoas \> Adicionar Pessoas**.
   
   ![Pessoas](./media/active-directory-saas-15five-tutorial/IC784676.png "Pessoas")
4. Na seção Adicionar Nova Pessoa, execute as etapas a seguir:
   
   ![Adicionar Nova Pessoa](./media/active-directory-saas-15five-tutorial/IC784677.png "Adicionar Nova Pessoa")
   
   1. Digite o **Nome**, **Sobrenome**, **Título** e **Endereço de email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Concluído**.
   
   > [!NOTE]
   > O titular da conta do Azure AD receberá um email com um link de confirmação de conta para que ela se torne ativa.
   > 
   > 



## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>Para atribuir usuários ao 15Five, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **15Five**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-15five-tutorial/IC784678.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-15five-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO5-->


