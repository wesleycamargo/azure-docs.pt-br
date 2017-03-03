---
title: "Tutorial: integração do Azure Active Directory ao Gigya | Microsoft Docs"
description: "Saiba como usar o Gigya com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: 96eb66f35a91e769418d179b0997cd32e02cdc97
ms.lasthandoff: 02/14/2017


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: integração do Active Directory do Azure ao Gigya
O objetivo deste tutorial é mostrar a integração do Azure ao Gigya.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Gigya

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Gigya poderão fazer logon único no aplicativo em seu site de empresa do Gigya (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para Gigya
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Configurar Logon Único](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurar Logon Único")

## <a name="enabling-the-application-integration-for-gigya"></a>Habilitando a integração de aplicativos para Gigya
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Gigya.

### <a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Gigya, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-gigya-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-gigya-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-gigya-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **Gigya**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-gigya-tutorial/IC789513.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **Gigya** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Gigya com a respectiva conta do Azure AD usando a federação baseada no protocolo SAML.  
Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base&64;.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Gigya**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurar Logon Único")

2. Na página **Como você deseja que os usuários façam logon no Gigya**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurar Logon Único")

3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do Gigya**, digite sua URL usando o padrão "*http://company.gigya.com*" e, em seguida, clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no Gigya**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurar Logon Único")

5. Em outra janela do navegador da Web, faça logon em seu site de empresa do Gigya como administrador.

6. Vá para **Configurações \> Logon do SAML** e clique no botão **Adicionar**.
   
    ![Logon SAML](./media/active-directory-saas-gigya-tutorial/IC789532.png "logon SAML")

7. Na seção **Logon SAML** , execute as seguintes etapas:
   
    ![Configuração SAML](./media/active-directory-saas-gigya-tutorial/IC789533.png "configuração SAML")
   
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.
   
    b. No portal clássico do Azure, na página do diálogo **Configurar logon único no Gigya**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor**.
   
    c. No portal clássico do Azure, na página do diálogo **Configurar logon único no Gigya**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL do Serviço de Logon Único**.
   
    d. No portal clássico do Azure, na página do diálogo **Configurar logon único no Gigya**, copie o valor da **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato da ID de Nome**.
   
    e. Crie um arquivo **codificado em base&64;** usando o certificado baixado.
      
    > [!TIP]
    > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509** .
   
    g. Clique em **Salvar Configurações**.

8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurar Logon Único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Gigya, eles devem ser provisionados no Gigya.  
No caso do Gigya, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Gigya** como administrador.
2. Acesse **Administrador \> Gerenciar Usuários** e clique em **Convidar Usuários**.
   
    ![Gerenciar Usuários](./media/active-directory-saas-gigya-tutorial/IC789535.png "Gerenciar Usuários")

3. Na caixa de diálogo Convidar Usuários, execute as seguintes etapas:
   
    ![Convidar Usuários](./media/active-directory-saas-gigya-tutorial/IC789536.png "Convidar Usuários")
   
    a. Na caixa de texto **Email** , digite o alias de email de uma conta válida do Active Directory do Azure que você deseja provisionar.
    
    b. Clique em **Convidar Usuário**.
      
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.
    > 
    > 

 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Para atribuir usuários ao Gigya, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Gigya**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-gigya-tutorial/IC789537.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-gigya-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


