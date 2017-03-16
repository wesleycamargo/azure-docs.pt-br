---
title: "Tutorial: integração do Azure Active Directory ao TimeOffManager | Microsoft Docs"
description: "Saiba como usar o TimeOffManager com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a028279bbc209b9bf9402813b687752f0320ce52
ms.openlocfilehash: 55a13e90ed5f8fd59a93bba507769e56594de568
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Tutorial: Integração do Active Directory do Azure ao TimeOffManager
O objetivo deste tutorial é mostrar a integração do Azure com o TimeOffManager.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do TimeOffManager habilitada para SSO (logon único)

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TimeOffManager poderão fazer logon com SSO no aplicativo em seu site de empresa do TimeOffManager (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos com o TimeOffManager
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Cenário")

## <a name="enable-the-application-integration-for-timeoffmanager"></a>Habilitar a integração de aplicativos para o TimeOffManager
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TimeOffManager.

**Para habilitar a integração de aplicativos para o TimeOffManager, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **TimeOffManager**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **TimeOffManager** e clique em **Concluir** para adicionar o aplicativo.
   
   ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

## <a name="configure-single-sign-on"></a>Configurar o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TimeOffManager com sua conta do AD do Azure usando federação baseada em protocolo SAML.  

Como parte desse procedimento, é necessário carregar um certificado codificado em base&64; no locatário do TimeOffManager. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração do aplicativo **TimeOffManager**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no TimeOffManager**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Resposta do TimeOffManager**, digite a URL de AssertionConsumerService do TimeOffManager (por exemplo: "*Example: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*" e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configurar URL do Aplicativo")
   
   Você pode obter a URL de resposta do logon único do TimeOffManager na página de configuração.
   
   ![Configurações de Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Configurações de Logon Único")
4. Na página **Configurar logon único no TimeOffManager**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configurar Logon Único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa TimeOffManager como um administrador.
6. Vá para **Conta \> Opções da Conta \> Configurações de Logon Único**.
   
   ![Configurações de Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Configurações de Logon Único")
7. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
   ![Configurações de Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Configurações de Logon Único")
  1.  Crie um arquivo **codificado em base&64;** usando o certificado baixado.  
   
       >[!TIP] 
       >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
       > 
  2.  Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
  3.  No portal clássico do Azure, na página de diálogo **Configurar logon único no TimeOffManager**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor IdP**.
  4.  No portal clássico do Azure, na página de diálogo **Configurar logon único no TimeOffManager**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL do Ponto de Extremidade do IdP**.
  5.  Para **Impor SAML**, selecione **Não**.
  6.  Para **Criação Automática de Usuários**, selecione **Sim**.
  7.  No Portal Clássico do Azure, na página de diálogo **Configurar logon único no TimeOffManager**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff** e clique em **Salvar Alterações**.

1. No portal clássico do Azure, na página de diálogo **Configurar logon único no TimeOffManager**, selecione a confirmação de configuração de logon único e clique em **Concluir**.
   
   ![Configurar Logon Único](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configurar Logon Único")
2. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** .
   
   ![Atributos](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Atributos")
3. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:
   
   ![Atributos de Token SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "Atributos de Token SAML")
   
   | Nome do atributo | Valor do atributo |
   | --- | --- |
   | Email |User.mail |
   | Firstname |User.givenname |
   | Sobrenome |User.surname |
  1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
  2.  Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
  3.  Na caixa de texto **Valor do Atributo** , selecione o valor do atributo mostrado para essa linha.
  4.  Clique em **Concluído**.
4. Clique em **Aplicar alterações**.

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para permitir que os usuários do AD do Azure façam logon no TimeOffManager, eles deverão ser provisionados no TimeOffManager.  

O TimeOffManager dá suporte ao provisionamento de usuário just in time. Não há nenhum item de ação para você.  

Os usuários são adicionados automaticamente durante o primeiro logon usando o logon único.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do TimeOffManager ou as APIs fornecidas pelo TimeOffManager para provisionar as contas de usuário do AAD.
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao TimeOffManager, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **TimeOffManager**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


