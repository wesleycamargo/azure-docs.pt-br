<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Replicon | Microsoft Azure" 
    description="Saiba como usar o Replicon com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Replicon
  
O objetivo deste tutorial é mostrar a integração do Azure com o Replicon. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Replicon
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Replicon poderão fazer logon único no aplicativo em seu site de empresa do Replicon (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Replicon
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-replicon-tutorial/IC777798.png "Cenário")
##Habilitando a integração de aplicativos para Replicon
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Replicon.

###Para habilitar a integração de aplicativos para o Replicon, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-replicon-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-replicon-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-replicon-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Replicon**.

    ![Galeria de aplicativos](./media/active-directory-saas-replicon-tutorial/IC777799.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Replicon** e clique em **Concluir** para adicionar o aplicativo.

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Replicon com sua conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Replicon**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Replicon**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto **URL de Entrada do Replicon**, digite a URL de locatário do Replicon (por exemplo: *https://na2.replicon.com/company/saml2/sp-sso/post*).
    2.  Na caixa de texto **URL de Resposta do Replicon**, digite a URL de **AssertionConsumerService** do Replicon (por exemplo: *https://global.replicon.com/!/saml2/company/sso/post*).

        >[AZURE.NOTE] Você pode obter a URL dos metadados do Replicon em: **https://global.replicon.com/!/saml2/\<ChaveSuaEmpresa>**.

    3.  Clique em **Próximo**.

4.  Na página **Configurar logon único no Replicon**, para baixar os metadados, clique em **Baixar metadados** e salve os metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configurar o logon único")

5.  Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Replicon como administrador.

6.  Para configurar o SAML 2.0, execute as seguintes etapas:

    ![Habilitar a autenticação do SAML](./media/active-directory-saas-replicon-tutorial/IC777805.png "Habilitar a autenticação do SAML")

    1.  Para exibir o diálogo **EnableSAML Authentication2**, acrescente o seguinte à URL, após a chave de sua empresa: **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2** Veja abaixo o esquema da URL completa: **https://na2.replicon.com/\<ChaveSuaEmpresa>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  Clique no **+** para expandir a seção **v20Configuration**.
    3.  Clique no **+** para expandir a seção **metaDataConfiguration**.
    4.  Clique em **Escolher Arquivo** para selecionar o arquivo XML de metadados de provedor de identidade e clique em **Enviar**.

7.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Replicon, eles deverão ser provisionados no Replicon. No caso do Replicon, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Em uma janela de navegador da Web, faça logon no site de sua empresa do Replicon como administrador.

2.  Vá para **Administração > Usuários**.

    ![Usuários](./media/active-directory-saas-replicon-tutorial/IC777806.png "Usuários")

3.  Clique em **+Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-replicon-tutorial/IC777807.png "Adicionar usuário")

4.  Na seção **Perfil de Usuário**, realize as seguintes etapas:

    ![Perfil de usuário](./media/active-directory-saas-replicon-tutorial/IC777808.png "Perfil de usuário")

    1.  Na caixa de texto **Nome de Logon**, digite o endereço de email do Azure AD do usuário do Azure AD que você deseja provisionar.
    2.  Para **Tipo de Autenticação**, selecione **SSO**.
    3.  Na caixa de texto **Departamento**, digite o departamento do usuário.
    4.  Para **Tipo de Funcionário**, selecione **Administrador**.
    5.  Clique em **Salvar Perfil do Usuário**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Replicon ou as APIs fornecidas pelo Replicon para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Replicon, execute as etapas a seguir:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Replicon**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-replicon-tutorial/IC777809.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-replicon-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0928_2016-->