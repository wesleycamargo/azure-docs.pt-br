<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Wikispaces | Microsoft Azure" description="Saiba como usar o Wikispaces com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Wikispaces
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528443).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Wikispaces. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Wikispaces com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Wikispaces poderão fazer um logon único no aplicativo no site da sua empresa Wikispaces (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Wikispaces
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Cenário")

##Habilitando a integração de aplicativos com o Wikispaces
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Wikispaces.

###Para habilitar a integração de aplicativos para o Wikispaces, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Wikispaces**.

    ![Galeria de Aplicativos](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Wikispaces** e clique em **Concluir** para adicionar o aplicativo.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem ao Wikispaces com sua conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo do **Wikispaces**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Configurar o logon único](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Wikispaces**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon no Wikispaces**, digite sua URL usando o seguinte padrão "**http://company.wikispaces.net*" e, em seguida, clique em **Próximo**.

    ![Configurar a URL do Aplicativo  
](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurar a URL do Aplicativo
")

4.  Na página **Configure o logon único no Wikispaces**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurar o logon único")

5.  Envie o metadatafile para a equipe de suporte do Wikispaces.

    >[AZURE.NOTE]A configuração de logon único deve ser executada pela equipe de suporte do Wikispaces. Assim que a configuração foi concluída, você receberá uma notificação.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Wikispaces, eles devem ser provisionados no Wikispaces. No caso do Wikispaces, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon no site da sua empresa **Wikispaces** como um administrador.

2.  Vá para **Membros**.

    ![Membros](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membros")

3.  Clique em **Convidar Pessoas**.

    ![Convidar pessoas](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Convidar pessoas")

4.  Na seção **Convidar Pessoas**, execute as seguintes etapas:

    ![Convidar pessoas](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Convidar pessoas")

    1.  Digite **Nomes de Usuário ou Endereço de Email** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Enviar**.  

        >[AZURE.NOTE]O titular da conta do Active Directory do Azure recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do Wikispaces ou APIs fornecidas pelo Wikispaces para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao Wikispaces, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Wikispaces **, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->