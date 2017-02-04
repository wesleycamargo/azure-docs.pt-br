---
title: "Tutorial: Integração do Azure Active Directory com o SugarCRM | Microsoft Docs"
description: "Saiba como usar o SugarCRM com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: f0e857dfa915d98beeeb06b224fbe761943dfbce


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Tutorial: Integração do Active Directory do Azure com o SugarCRM
O objetivo deste tutorial é mostrar a integração do Azure com o SugarCRM.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Sugar CRM habilitada para logon único

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SugarCRM poderão fazer logon único no aplicativo em seu site de empresa do SugarCRM (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o SugarCRM
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Cenário")

## <a name="enabling-the-application-integration-for-sugar-crm"></a>Habilitando a integração de aplicativos para o SugarCRM
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SugarCRM.

### <a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o SugarCRM, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **SugarCRM**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **SugarCRM** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SugarCRM com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte deste procedimento, será necessário carregar um certificado codificado em base&64; no locatário do SugarCRM.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal clássico do Azure, na página de integração do aplicativo **Sugar CRM**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurar Logon Único")

2. Na página **Como você deseja que os usuários façam logon no Sugar CRM**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurar Logon Único")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Sugar CRM**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Sugar CRM (por exemplo: "*http://company.sugarondemand.com*" e clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no Sugar CRM**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurar Logon Único")

5. Em outra janela do navegador da Web, faça logon em seu site de empresa SugarCRM como um administrador.

6. Vá para **Administrador**.
   
    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

7. Na seção **Administração**, clique em **Gerenciamento de Senhas**.
   
    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administração")

8. Selecione **Habilitar Autenticação SAML**.
   
    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administração")

9. Na seção **Autenticação SAML** , realize as seguintes etapas:
   
    ![Autenticação SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticação SAML")
   
    a. No Portal clássico do Azure, na página de diálogo **Configurar logon único no Sugar CRM**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   
    b. No Portal clássico do Azure, na página do diálogo **Configurar logon único no Sugar CRM**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de SLO**.
   
    c. Crie um arquivo **codificado em base&64;** usando o certificado baixado.
      
    > [!TIP]
    > Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    d. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
   
    e. Clique em **Salvar**.

10. No Portal clássico do Azure, na página de diálogo **Configurar logon único no Sugar CRM**, selecione a confirmação de configuração de logon único e clique em **Concluir**.
    
    ![Configurar Logon Único](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurar Logon Único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no SugarCRM, eles deverão ser provisionados no SugarCRM.  
No caso do SugarCRM, o provisionamento será uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **SugarCRM** como administrador.

2. Vá para **Administrador**.
   
    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

3. Na seção **Administração**, clique em **Gerenciamento de Usuários**.
   
    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administração")

4. Vá para **Usuários \> Criar Novo Usuário**.
   
    ![Criar Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Criar Novo Usuário")

5. Na guia **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Novo Usuário")
   
    a. Digite o nome de usuário, o sobrenome e o endereço de email de um usuário válido do Active Directory do Azure nas caixas de texto relacionadas.

6. Para **Status**, selecione **Ativo**.

7. Na guia Senha, execute as seguintes etapas:
   
    ![Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Novo Usuário")
   
    a. Digite a senha na caixa de texto relacionada.
   
    b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SugarCRM ou as APIs fornecidas pelo SugarCRM para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Para atribuir usuários ao SugarCRM, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Sugar CRM**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->


