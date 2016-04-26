<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao ScreenSteps | Microsoft Azure" 
    description="Saiba como usar o ScreenSteps com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o ScreenSteps
  
O objetivo deste tutorial é mostrar a integração do Azure ao ScreenSteps. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do ScreenSteps
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ScreenSteps poderão fazer logon único no aplicativo em seu site de empresa do ScreenSteps (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o ScreenSteps
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Cenário")
##Habilitando a integração de aplicativos para o ScreenSteps
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ScreenSteps.

###Para habilitar a integração de aplicativos com o ScreenSteps, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ScreenSteps**.

    ![Galeria de aplicativos](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ScreenSteps** e clique em **Concluir** para adicionar o aplicativo.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ScreenSteps com sua conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **ScreenSteps**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no ScreenSteps**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do ScreenSteps**, digite a URL usando o padrão "*https://\<nome-locatário>.ScreenSteps.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no ScreenSteps**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa ScreenSteps como um administrador.

6.  Clique em **Gerenciamento de Contas**.

    ![Gerenciamento de contas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gerenciamento de contas")

7.  Clique em **Autenticação Remota**.

    ![Autenticação remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticação remota")

8.  Clique em **Criar ponto de extremidade de autenticação**.

    ![Autenticação remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticação remota")

9.  Na seção **Criar um Ponto de Extremidade de Autenticação**, realize as seguintes etapas:

    ![Criar um ponto de extremidade de autenticação](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Criar um ponto de extremidade de autenticação")

    1.  Na caixa de texto **Título**, digite um título.
    2.  Na lista **Modo**, selecione **SAML**.
    3.  Clique em **Criar**.

10. Na seção **Ponto de Extremidade de Autenticação Remota**, realize as seguintes etapas:

    ![Ponto de extremidade de autenticação remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Ponto de extremidade de autenticação remota")

    1.  No portal do Azure AD, na página **Configurar logon único no ScreenSteps**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon Remoto**.
    2.  No portal do Azure AD, na página **Configurar logon único no ScreenSteps**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    3.  Clique em **Escolher um arquivo** e carregue o certificado baixado.
    4.  Clique em **Atualizar**.

11. No portal do AD do Azure, selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no **ScreenSteps**, eles devem ser provisionados no **ScreenSteps**. No caso do **ScreenSteps**, o provisionamento é uma tarefa manual.

###Para provisionar uma conta de usuário no ScreenSteps, execute as seguintes etapas:

1.  Faça logon em seu locatário do **ScreenSteps**.

2.  Clique em **Gerenciamento de Contas**.

    ![Gerenciamento de contas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gerenciamento de contas")

3.  Clique em **Usuários**.

    ![Usuários](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Usuários")

4.  Clique em **Criar um usuário**.

    ![Todos os Usuários](./media/active-directory-saas-screensteps-tutorial/IC778545.png "Todos os Usuários")

5.  Na lista **Função de Usuário**, selecione uma função para o usuário.

6.  Na seção Função de Usuário, digite o “**Nome**, **Sobrenome**, **Email**, **Logon**, **Senha** e **Configuração da Senha**” de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.

    ![Novo usuário](./media/active-directory-saas-screensteps-tutorial/IC778546.png "Novo usuário")

7.  Na seção Grupos, selecione **Grupo de Autenticação (SAML)** e clique em **Criar Usuário**.

    ![Grupos](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupos")

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do ScreenSteps ou as APIs fornecidas pelo ScreenSteps para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao ScreenSteps, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ScreenSteps**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Atribuir usuários](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Atribuir usuários")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0413_2016-->