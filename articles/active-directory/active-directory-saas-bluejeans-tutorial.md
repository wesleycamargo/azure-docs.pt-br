<properties 
    pageTitle="Tutorial: integração do Azure Active Directory ao BlueJeans | Microsoft Azure" 
    description="Saiba como usar o BlueJeans com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do AD do Azure ao BlueJeans

O objetivo deste tutorial é mostrar a integração do Azure ao BlueJeans. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do BlueJeans

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao BlueJeans poderão fazer logon único no aplicativo em seu site de empresa do BlueJeans (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o BlueJeans
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Cenário")
##Habilitando a integração de aplicativos para o BlueJeans

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o BlueJeans.

###Para habilitar a integração de aplicativos para o BlueJeans, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **BlueJeans**.

    ![Galeria de Aplicativos](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **BlueJeans** e clique em **Concluir** para adicionar o aplicativo.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no BlueJeans com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **BlueJeans**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no BlueJeans**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do BlueJeans**, digite a URL usando o padrão "**https://company.BlueJeans.com**" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no BlueJeans**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do **BlueJeans** como administrador.

6.  Vá para **ADMINISTRADOR > Configurações de Grupo > Segurança**.

    ![Administrador](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrador")

7.  Na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único do SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Logon Único do SAML")

    1.  Selecione **Logon Único do SAML**.
    2.  Selecione **Habilitar provisionamento automático**.

8.  Siga em frente com as seguintes etapas:

    ![Caminho do Certificado](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Caminho do Certificado")

    1.  Clique em **Escolher Arquivo** e carregue o certificado baixado.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no BlueJeans**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no BlueJeans**, copie o valor da **URL de Alteração de Senha** e cole-o na caixa de texto **URL de Alteração de Senha**.
    4.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no BlueJeans**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.

9.  Siga em frente com as seguintes etapas:

    ![Salvar Alterações](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Salvar Alterações")

    1.  Na caixa de texto **ID do usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
	2.  Na caixa de texto **Email**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
	3.  Clique em **Salvar Alterações**.

10. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no BlueJeans, eles devem ser provisionados no BlueJeans. No caso do BlueJeans, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **BlueJeans** como administrador.

2.  Vá para **ADMINISTRADOR > Gerenciar Usuários > Adicionar Usuário**.

    ![Administrador](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrador")

    >[AZURE.IMPORTANT] A guia **Adicionar Usuário** só estará disponível se, na **guia Segurança**, a opção **Habilitar provisionamento automático** estiver desmarcada.

3.  Na seção **Adicionar Usuário**, realize as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Adicionar usuário")

    1.  Digite um **Nome de Usuário do BlueJeans**, **Endereço de email**, **ID de Reunião do BlueJeans**, **Senha de Moderador**, **Nome Completo** e a **Empresa** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Adicionar Usuário**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans ou as APIs fornecidas pelo BlueJeans para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao BlueJeans, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **BlueJeans**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!----HONumber=AcomDC_0413_2016-->