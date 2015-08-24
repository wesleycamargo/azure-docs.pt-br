<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao SmarterU | Microsoft Azure" description="Saiba como usar o SmarterU com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o SmarterU
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524463).
  
O objetivo deste tutorial é mostrar a integração do Azure com o SmarterU. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do SmarterU
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao SmarterU poderão fazer logon único no aplicativo em seu site de empresa SmarterU (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o SmarterU
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Cenário")

##Habilitando a integração de aplicativos para o SmarterU
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SmarterU.

###Para habilitar a integração de aplicativos com o SmarterU, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **SmarterU**.

    ![Falhas de aplicativo](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Falhas de aplicativo")

7.  No painel de resultados, selecione **SmarterU** e clique em **Concluir** para adicionar o aplicativo.

    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SmarterU com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **SmarterU**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no SmarterU**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar o logon único")

3.  Na página **Configurar logon único no SmarterU**, para baixar seus metadados, clique em **Baixar metadados** e salve o arquivo de dados localmente como **c:\\SmarterUMetaData.cer**.

    ![Configurar o logon único](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar o logon único")

4.  Em outra janela do navegador da Web, faça logon em seu site de empresa SmarterU como um administrador.

5.  Na barra de ferramentas na parte superior, clique em **Configurações de Conta**.

    ![Configurações da Conta](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Configurações da Conta")

6.  Na página de configuração da conta, execute as seguintes etapas:

    ![Autoridade Externa](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autoridade Externa")

    1.  Selecione **Habilitar Autorização Externa**.
    2.  Na seção **Controle de Logon Mestre**, selecione a guia **SmarterU**.
    3.  Na seção **Logon Padrão de Usuário**, selecione a guia **SmarterU**.
    4.  Selecione **Habilitar Okta**.
    5.  Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **Metadados do Okta**.
    6.  Clique em **Salvar**.

7.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SmarterU, eles deverão ser provisionados no SmarterU. No caso do SmarterU, o provisionamento será uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon no seu locatário do **SmarterU**.

2.  Vá para **Usuários**.

3.  Na seção do usuário, execute as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Novo Usuário")

    1.  Clique em **+Usuário**.
    2.  Digite os valores de atributo relacionados da conta de usuário do AD do Azure nas seguintes caixas de texto:**Email Principal**, **ID do Funcionário**, **Senha**, **Verificar Senha**, **Nome Dado**, **Sobrenome**.
    3.  Clique em **Ativo**.
    4.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do SmarterU ou as APIs fornecidas pelo SmarterU para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao SmarterU, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **SmarterU**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sim")
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->