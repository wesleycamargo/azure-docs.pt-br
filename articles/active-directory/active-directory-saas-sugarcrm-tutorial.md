<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o SugarCRM | Microsoft Azure" 
    description="Saiba como usar o SugarCRM com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure com o SugarCRM
  
O objetivo deste tutorial é mostrar a integração do Azure com o SugarCRM. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do SugarCRM com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SugarCRM poderão fazer logon único no aplicativo em seu site de empresa do SugarCRM (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o SugarCRM
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Cenário")

##Habilitando a integração de aplicativos para o SugarCRM
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SugarCRM.

###Para habilitar a integração de aplicativos com o SugarCRM, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **SugarCRM**.

    ![Galeria de Aplicativos](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **SugarCRM** e clique em **Concluir** para adicionar o aplicativo.

    ![SugarCRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "SugarCRM")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SugarCRM com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte deste procedimento, será necessário carregar um certificado codificado em base 64 no locatário do SugarCRM. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **SugarCRM**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Sugar CRM**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Sugar CRM**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Sugar CRM (por exemplo: "**http://company.sugarondemand.com*") e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Sugar CRM**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa SugarCRM como um administrador.

6.  Vá para **Administrador**.

    ![Administrador](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrador")

7.  Na seção **Administração**, clique em **Gerenciamento de Senhas**.

    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administração")

8.  Selecione **Habilitar Autenticação SAML**.

    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administração")

9.  Na seção **Autenticação SAML**, realize as seguintes etapas:

    ![Autenticação do SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticação do SAML")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Sugar CRM**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Sugar CRM**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de SLO**.
    3.  Crie um arquivo **codificado em base 64** usando o certificado baixado.

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509**.
    5.  Clique em **Salvar**.

10. No portal do Azure, na página do diálogo **Configurar logon único no Sugar CRM**, selecione a confirmação de configuração de logon único e clique em **Concluir**.

    ![Configurar o logon único](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SugarCRM, eles deverão ser provisionados no SugarCRM. No caso do SugarCRM, o provisionamento será uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **SugarCRM** como administrador.

2.  Vá para **Administrador**.

    ![Administrador](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administrador")

3.  Na seção **Administração**, clique em **Gerenciamento de Usuários**.

    ![Administração](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administração")

4.  Vá para **Usuários > Criar Novo Usuário**.

    ![Criar Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Criar Novo Usuário")

5.  Na guia **Perfil do Usuário**, realize as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Novo Usuário")

    1.  Digite o nome de usuário, o sobrenome e o endereço de email de um usuário válido do Active Directory do Azure nas caixas de texto relacionadas.

6.  Para **Status**, selecione **Ativo**.

7.  Na guia Senha, execute as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Novo Usuário")

    1.  Digite a senha na caixa de texto relacionada.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do SugarCRM ou as APIs fornecidas pelo SugarCRM para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao SugarCRM, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Sugar CRM**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->