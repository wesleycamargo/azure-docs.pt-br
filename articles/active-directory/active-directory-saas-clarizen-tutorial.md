<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Clarizen | Microsoft Azure" 
    description="Saiba como usar o Clarizen com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao Clarizen

O objetivo deste tutorial é mostrar a integração do Azure ao Clarizen. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Clarizen

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Clarizen poderão fazer logon único no aplicativo em seu site de empresa do Clarizen (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Clarizen
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Cenário")
##Habilitando a integração de aplicativos para o Clarizen

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Clarizen.

###Para habilitar a integração de aplicativos para o Clarizen, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Clarizen**.

    ![Galeria de Aplicativos](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Clarizen** e clique em **Concluir** para adicionar o aplicativo.

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Clarizen com a própria conta no Azure AD usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração do aplicativo **Clarizen**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Clarizen**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurar o logon único")

3.  Na página **Configurar logon único no Clarizen**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurar o logon único")

4.  Em outra janela do navegador da Web, faça logon em seu site de empresa do **Clarizen** como administrador (por exemplo: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Clique no nome de usuário e em **Configurações**.

    ![Configurações](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Configurações")

6.  Clique na guia **Configurações Globais** e, ao lado de **Autenticação Federada**, clique em **editar**.

    ![Configurações Globais](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Configurações Globais")

7.  No diálogo **Autenticação Federada**, realize as seguintes etapas:

    ![Autenticação Federada](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Autenticação Federada")

    1.  Clique em **Carregar** para carregar o certificado baixado.
    2.  No portal clássico do Azure, na página do diálogo **Configurar logon único no Clarizen**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Entrada**.
    3.  No portal clássico do Azure, na página do diálogo **Configurar logout único no Clarizen**, copie o valor da **URL do Serviço de Logout Único** e cole-o na caixa de texto **URL de Logout**.
    4.  Selecione **Usar POST**.
    5.  Clique em **Salvar**.

8.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Clarizen, eles deverão ser provisionados no Clarizen. No caso do Clarizen, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Clarizen** como administrador.

2.  Clique em **Pessoas**.

    ![Pessoas](./media/active-directory-saas-clarizen-tutorial/IC784689.png "Pessoas")

3.  Clique em **Convidar Usuário**.

    ![Convidar Usuários](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Convidar Usuários")

4.  Na página Convidar Pessoas, execute as seguintes etapas:

    ![Convidar Pessoas](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Convidar Pessoas")

    1.  Na caixa de texto **Email**, digite o endereço de email de uma conta válida do Active Directory do Azure que você deseja provisionar.
    2.  Clique em **Convidar**.

    >[AZURE.NOTE] O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Clarizen, execute as etapas a seguir:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração do aplicativo **Clarizen**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->