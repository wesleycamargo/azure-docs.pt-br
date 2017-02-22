---
title: "Tutorial: Integração do Azure Active Directory com o Work.com | Microsoft Docs"
description: "Saiba como usar o Work.com com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 69d94659f4eff72e1c449fd915616d81fd4712de


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Active Directory do Azure com o Work.com
O objetivo deste tutorial é mostrar a integração do Azure com o Work.com.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Work.com habilitada para logon único

Depois de concluir este tutorial, os usuários do AAD aos quais você atribuiu acesso ao Work.com poderão fazer logon único no aplicativo em seu site de empresa do Work.com (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Work.com
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

## <a name="enabling-the-application-integration-for-workcom"></a>Habilitando a integração de aplicativos para o Work.com
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Work.com.

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Work.com, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **Work.com**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")

7. No painel de resultados, selecione **Work.com** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Work.com com sua conta do AD do Azure usando federação baseada em protocolo de SAML.  
Como parte desse procedimento, é necessário carregar um certificado para o Work.com.

> [!NOTE]
> Para configurar o logon único, você ainda precisa definir um nome de domínio personalizado Work.com. Você precisa definir pelo menos um nome de domínio, testá-lo e implantá-lo em toda a sua organização.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. Faça logon no seu locatário Work.com como administrador.
2. Vá para **Configuração**.
   
    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
   
    ![Meu domínio](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")

4. Para verificar se seu domínio foi configurado corretamente, veja se ele está em "**Etapa 4 Implantado para Usuários**" e revise "**Minhas Configurações de Domínio**".
   
    ![Domínio implantado para o usuário](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")

5. Em uma janela de navegador da Web diferente, faça logon no portal clássico do Azure.

6. Na página de integração de aplicativos do **Work.com**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")

7. Na página **Como você deseja que os usuários façam logon no Work.com**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")

8. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Work.com**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Work.com (por exemplo: “*http://company.my.salesforce.com*”) e clique em **Avançar**: 
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")

9. Na página **Configurar logon único no Work.com**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")

10. Faça logon no seu locatário Work.com.

11. Vá para **Configuração**.
    
    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

12. Expanda o menu **Controles de Segurança** e clique em **Configurações de Logon Único**.
    
    ![Configurações de Logon Único](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")

13. Na página do diálogo **Configurações de Logon Único** , realize as seguintes etapas:
    
    ![SAML Habilitado](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")
    
    a. Selecione **SAML Habilitado**.
    
    b. Clique em **Novo**.

14. Na seção de **Configurações de Logon Único do SAML** , realize as seguintes etapas:
    
    ![Configuração de Logon Único do SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")
    
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **Nome** popula automaticamente a caixa de texto **Nome da API**.
    > 
    > 
    
    b. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Work.com**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor**.
    
    c. Para carregar o certificado baixado, clique em **Procurar**.
    
    d. Na caixa de texto **ID da Entidade**, digite **https://salesforce-work.com**.
    
    e. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**.
    
    f. Para **Local de Identidade do SAML**, selecione **A identidade está contida no elemento NameIdentifier da instrução Subject**.
    
    g. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Work.com**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL do Logon do Provedor de Identidade**.
    
    h. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Work.com**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **URL do Logoff do Provedor de Identidade**.
    
    i. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP Post**.
    
    j. Clique em **Salvar**.

15. No painel de navegação à esquerda, no portal clássico do Work.com, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
    
    ![Meu domínio](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")

16. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.
    
    ![Identidade visual da página de logon](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")

17. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.
    
    ![Identidade visual da página de logon](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")

18. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para que os usuários do Active Directory do Azure possam entrar, eles devem ser provisionados para Work.com.  
No caso do Work.com, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon no site da sua empresa do Work.com como um administrador.

2. Vá para **Configuração**.
   
    ![Configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. Vá para **Gerenciar Usuários \> Usuários**.
   
    ![Gerenciar Usuários](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4. Clique em **Novo Usuário**.
   
    ![Todos os Usuários](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5. Na seção Editar Usuário, execute as seguintes etapas:
   
    ![Editar usuário](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")
   
    a. Digite os atributos **Sobrenome**, **Alias**, **Email**, **Nome de Usuário** e **Apelido** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Selecione **Função**, **Licença de Usuário** e **Perfil**.
   
    c. Clique em **Salvar**.  
      
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    > 
    > 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Para atribuir usuários ao Work.com, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do Work.com, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")

Agora, você deveria aguardar 10 minutos e verificar se a conta foi sincronizada com o Work.com.

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


