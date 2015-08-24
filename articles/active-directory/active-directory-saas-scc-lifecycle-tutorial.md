<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o SCC LifeCycle | Microsoft Azure" description="Saiba como usar o SCC LifeCycle com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o SCC LifeCycle
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=533911).
  
O objetivo deste tutorial é mostrar a integração do Azure com o SCC LifeCycle. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do SCC LifeCycle
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao SCC LifeCycle poderão fazer logon único no aplicativo em seu site de empresa SCC LifeCycle (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o SCC LifeCycle
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Cenário")
##Habilitando a integração de aplicativos com o SCC LifeCycle
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o SCC LifeCycle.

###Para habilitar a integração de aplicativos com o SCC LifeCycle, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **SCC LifeCycle**.

    ![Galeria de Aplicativos](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **SCC LifeCycle** e clique em **Concluir** para adicionar o aplicativo.

    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SCC LifeCycle com sua conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **SCC LifeCycle**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único **.

    ![Configurar o logon único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no SCC LifeCycle**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon**, digite a URL usada por seus usuários para fazer logon no aplicativo SCC LifeCycle usando o padrão “**https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no SCC LifeCycle**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar o logon único")

5.  Encaminhe esse arquivo de Metadados à equipe de Suporte do SCC LifeCycle.

    >[AZURE.NOTE]O logon único deve ser habilitado pela equipe de suporte do SCC LifeCycle.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SCC LifeCycle, eles deverão ser provisionados no SCC LifeCycle.
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o SCC LifeCycle. Quando um usuário atribuído tentar fazer logon no SCC LifeCycle, uma conta do SCC LifeCycle será automaticamente criada, se necessário.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do SCC LifeCycle ou as APIs fornecidas pelo SCC LifeCycle para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao SCC LifeCycle, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **SCC LifeCycle**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sim")
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->