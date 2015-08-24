<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Mozy Enterprise | Microsoft Azure" description="Saiba como usar o Mozy Enterprise com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Mozy Enterprise
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524186).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Mozy Enterprise. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Mozy Enterprise
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Mozy Enterprise poderão fazer logon único no aplicativo em seu site de empresa do Mozy Enterprise (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Mozy Enterprise
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Cenário")
##Habilitando a integração de aplicativos com o Mozy Enterprise
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Mozy Enterprise.

###Para habilitar a integração de aplicativos com o Mozy Enterprise, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **mozy enterprise**.

    ![Galeria de Aplicativos](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Mozy Enterprise** e clique em **Concluir** para adicionar o aplicativo.

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Mozy Enterprise com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário carregar um certificado codificado em base 64 no locatário do Mozy Enterprise. Se você não estiver familiarizado com este procedimento, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Mozy Enterprise**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Mozy Enterprise**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de entrada do Mozy Enterprise**, digite sua URL usando o seguinte padrão "*https://\<nome do locatário>.Mozyenterprise.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Mozy Enterprise**, para baixar seu certificado, clique em **Baixar certificado** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Mozy Enterprise como administrador.

6.  Na seção **Configuração**, clique em **Política de Autenticação**.

    ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Política de autenticação")

7.  Na seção **Política de Autenticação**, execute as seguintes etapas:

    ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Política de autenticação")

    1.  Selecione **Serviço de Diretório** como **Provedor**.
    2.  Selecione **Usar LDAP Push**.
    3.  Clique na guia **Autenticação do SAML**.
    4.  No portal do Azure, na página de diálogo **Configurar logon único no Mozy Enterprise**, copie o valor da **URL da solicitação de autenticação** e cole-o na caixa de texto **URL de autenticação**.
    5.  No portal do Azure, na página de diálogo **Configurar logon único no Mozy Enterprise**, copie o valor da **Id do Provedor de Identidade** e cole-o na caixa de texto **Ponto de Extremidade SAML**.
    6.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    7.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole todo o Certificado na caixa de texto **Certificado SAML**
    8.  Selecione **Habilitar SSO para que Administradores façam logon com suas credenciais de rede**.
    9.  Clique em **Salvar Alterações**.

8.  No portal do Azure, na página **Configurar logon único no Mozy Enterprise**, selecione a confirmação de configuração de logon único e clique em **Concluir**.

    ![Configurar o logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Mozy Enterprise, eles devem ser provisionados no Mozy Enterprise. No caso do Mozy Enterprise, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon no seu locatário do **Mozy Enterprise**.

2.  Clique em **Usuários** e, em seguida, clique em **Adicionar Novo Usuário**.

    ![Usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Usuários")

    >[AZURE.NOTE]A opção **Adicionar Novo Usuário** só será exibida se **Mozy**estiver selecionado como o provedor em **Política de autenticação**. Se a autenticação SAML for configurada, os usuários serão adicionados automaticamente em seu primeiro logon por meio do Logon único.

3.  Na caixa de diálogo novo usuário, execute as seguintes etapas:

    ![Adicionar Usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Adicionar Usuários")

    1.  Na lista **Escolher um Grupo**, selecione um grupo.
    2.  Na lista **Wue tipo de usuário**, selecione um tipo.
    3.  Na caixa de texto **Nome do Usuário**, digite o nome do usuário do AD do Azure.
    4.  Na caixa de texto **Email**, digite o endereço de email do usuário do AD do Azure.
    5.  Selecione **Enviar email de instruções do usuário**.
    6.  Clique em **Adicionar Usuário(s)**.

    >[AZURE.NOTE]Após a criação do usuário, um email será enviado ao usuário do AD do Azure com um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Mozy Enterprise ou as APIs fornecidas pelo Mozy Enterprise para provisionar as contas de usuário do AAD.

##Atribuindo usuários
 
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Mozy Enterprise, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Mozy Enterprise**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Sim")
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->