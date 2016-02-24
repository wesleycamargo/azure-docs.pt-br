<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao FM:Systems | Microsoft Azure" 
    description="Saiba como usar o FM:Systems com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure ao FM:Systems
  
O objetivo deste tutorial é mostrar a integração do Azure ao FM:Systems. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do FM:Systems
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao FM:Systems poderão fazer logon único no aplicativo em seu site de empresa do FM:Systems (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o FM:Systems
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Cenário")
##Habilitando a integração de aplicativos para o FM:Systems
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o FM:Systems.

###Para habilitar a integração de aplicativos com o FM:Systems, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **FM:Systems**.

    ![Galeria de Aplicativos](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **FM:Systems** e clique em **Concluir** para adicionar o aplicativo.

    ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no FM:Systems com a própria conta do Azure AD usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **FM:Systems**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no FM:Systems**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Entrada do FM:Systems**, digite a **URL de Resposta** do FM:Systems (por exemplo: **https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*)).

        >[AZURE.WARNING]Você pode obter esse valor com a equipe de suporte do FM:Systems.

    2.  Clique em **Próximo**.

4.  Na página **Configurar logon único no FM:Systems**, para baixar os metadados, clique em **Baixar metadados** e salve os metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar o logon único")

5.  Envie o arquivo de metadados baixado para a equipe de suporte do FM:Systems.

    >[AZURE.NOTE]A equipe de suporte do FM:Systems precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no FM:Systems, eles devem ser provisionados no FM:Systems. No caso do FM:Systems, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do FM:Systems como administrador.

2.  Vá para **Administração do Sistema > Gerenciar Segurança > Usuários > Lista de usuários**.

    ![Administração do Sistema](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administração do Sistema")

3.  Clique em **Criar novo usuário**.

    ![Criar Novo Usuário](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Criar Novo Usuário")

4.  Na seção **Criar Usuário**, realize as seguintes etapas:

    ![Criar Usuário](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Criar Usuário")

    1.  Digite o nome de usuário, a senha e sua confirmação, o endereço de email e a ID de funcionário de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Próximo**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do FM:Systems ou as APIs fornecidas pelo FM:Systems para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao FM:Systems, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do ** FM:Systems**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->