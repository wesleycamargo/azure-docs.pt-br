<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Pagerduty | Microsoft Azure" 
    description="Saiba como usar o Pagerduty com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure com o Pagerduty
  
O objetivo deste tutorial é mostrar a integração do Azure com o Pagerduty. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Pagerduty
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Pagerduty poderão fazer logon único no aplicativo em seu site de empresa do Pagerduty (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Pagerduty
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Cenário")
##Habilitando a integração de aplicativos para o Pagerduty
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Pagerduty.

###Para habilitar a integração de aplicativos para o Pagerduty, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Pagerduty**.

    ![Galeria de aplicativos](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Pagerduty** e clique em **Concluir** para adicionar o aplicativo.

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Pagerduty com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Pagerduty**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Pagerduty**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Pagerduty**, digite a URL usando o padrão "*https://\<nome-locatário>.Pagerduty.com*" e clique em **Avançar**.

    ![Configurar a url do aplicativo](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurar a url do aplicativo")

4.  Na página **Configurar logon único no Pagerduty**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do Pagerduty como administrador.

6.  No menu na parte superior, clique em **Configurações da Conta**.

    ![Configurações da Conta](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Configurações da Conta")

7.  Clique em **logon único**.

    ![Logon único](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Logon único")

8.  Na página Habilitar Logon Único (SSO), execute as seguintes etapas:

    ![Habilitar logon único](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Habilitar logon único")

    1.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Pagerduty**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    4.  No portal do Azure, na página do diálogo **Configurar logon único no Pagerduty**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    5.  Selecione **Ativar Logon Único**.
    6.  Clique em **Salvar Alterações**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Pagerduty, eles devem ser provisionados no Pagerduty. No caso do Pagerduty, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Pagerduty**.

2.  No menu na parte superior, clique em **Usuários**.

3.  Clique em **Adicionar Usuários**.

    ![Adicionar Usuários](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Adicionar Usuários")

4.  No diálogo **Convidar sua equipe**, digite o **Nome e Sobrenome** e o endereço de **Email** do usuário do Azure AD que você deseja configurar, clique em **Adicionar** e em **Enviar Convites**.

    ![Convidar sua equipe](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Convidar sua equipe")

    >[AZURE.NOTE]Todos os usuários adicionados receberão um convite para criar uma conta do PagerDuty.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Pagerduty ou as APIs fornecidas pelo Pagerduty para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Pagerduty, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Pagerduty**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->