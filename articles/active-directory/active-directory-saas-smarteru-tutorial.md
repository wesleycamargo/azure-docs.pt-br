---
title: "Tutorial: integração do Azure Active Directory com o SmarterU | Microsoft Docs"
description: "Saiba como usar o SmarterU com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: 058f84f57c18735d42b95a74aee74468ccef0f8d


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: Integração do Active Directory do Azure com o SmarterU
O objetivo deste tutorial é mostrar a integração do Azure com o SmarterU.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do SmarterU

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SmarterU poderão fazer logon único no aplicativo em seu site de empresa do SmarterU (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o SmarterU
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Cenário")

## <a name="enabling-the-application-integration-for-smarteru"></a>Habilitando a integração de aplicativos para o SmarterU
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SmarterU.

### <a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o SmarterU, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **SmarterU**.
   
    ![Falhas de aplicativo](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Falhas de aplicativo")

7. No painel de resultados, selecione **SmarterU**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SmarterU com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **SmarterU**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar Logon Único")

2. Na página **Como você gostaria que os usuários fizessem logon no SmarterU**, selecione **Logon Único do Microsoft Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar Logon Único")

3. Na página **Configurar logon único no SmarterU**, para baixar seus metadados, clique em **Baixar metadados** e no arquivo de dados local, como **c:\\SmarterUMetaData.cer**.
   
    ![Configurar Logon Único](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar Logon Único")

4. Em outra janela do navegador da Web, faça logon em seu site de empresa SmarterU como um administrador.

5. Na barra de ferramentas na parte superior, clique em **Configurações da Conta**.
   
    ![Configurações de Conta](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Configurações de Conta")

6. Na página de configuração da conta, execute as seguintes etapas: 
   
    ![Autorização Externa](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorização Externa")
 
    a. Selecione **Habilitar Autorização Externa**.
   
    b. Na seção **Controle de Logon Principal**, selecione a guia **SmarterU**.
   
    c. Na seção **Logon do Usuário Padrão**, selecione a guia **SmarterU**.
   
    d. Selecione **Habilitar Okta**.
   
    e. Copie o conteúdo do arquivo de metadados baixado e cole-o na caixa de texto **Metadados do Okta** .

    f. Clique em **Salvar**.

7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar Logon Único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no SmarterU, eles deverão ser provisionados no SmarterU.  
No caso do SmarterU, o provisionamento será uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **SmarterU** .

2. Vá para **Usuários**.

3. Na seção do usuário, execute as seguintes etapas:
   
    ![Novo Usuário](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Novo Usuário")
   
    a. Clique em **+Usuário**.
   
    b. Digite os valores de atributo relacionados da conta de usuário do Azure AD nas seguintes caixas de texto: **Email Principal**, **ID do Funcionário**, **Senha**, **Verificar Senha**, **Nome Dado**, **Sobrenome**.
   
    c. Clique em **Ativo**.
   
    d. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SmarterU ou as APIs fornecidas pelo SmarterU para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Para atribuir usuários ao SmarterU, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração do aplicativo **SmarterU**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->


