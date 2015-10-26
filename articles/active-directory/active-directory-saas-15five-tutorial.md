<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao 15Five | Microsoft Azure" description="Saiba como usar o 15Five com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao 15Five
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528017).

O objetivo deste tutorial é mostrar a integração do Azure ao 15Five. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do 15Five

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao 15Five poderão fazer logon único no aplicativo em seu site de empresa 15Five (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o 15Five
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-15five-tutorial/IC784667.png "Cenário")
##Habilitando a integração de aplicativos para o 15Five

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o 15Five.

###Para habilitar a integração de aplicativos para o 15Five, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-15five-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-15five-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **15Five**.

    ![Galeria de Aplicativos](./media/active-directory-saas-15five-tutorial/IC784668.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **15Five** e clique em **Concluir** para adicionar o aplicativo.

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no 15Five com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **15Five**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no 15Five**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon no 15Five**, digite sua URL usando o seguinte padrão "**https://company.15Five.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar logon único no 15Five**, clique em **Baixar metadados** e encaminhe o arquivo de metadados para a equipe de suporte do 15Five.

    ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar logon único")

    >[AZURE.NOTE]O logon único precisa ser habilitado pela equipe de suporte do 15Five.

5.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no 15Five, eles devem ser provisionados no 15Five. No caso do 15Five, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **15Five** como um administrador.

2.  Vá para **Gerenciar Empresa**.

    ![Gerenciar Empresa](./media/active-directory-saas-15five-tutorial/IC784675.png "Gerenciar Empresa")

3.  Vá para **Pessoas > Adicionar Pessoas**.

    ![Pessoas](./media/active-directory-saas-15five-tutorial/IC784676.png "Pessoas")

4.  Na seção Adicionar Nova Pessoa, execute as etapas a seguir:

    ![Adicionar Nova Pessoa](./media/active-directory-saas-15five-tutorial/IC784677.png "Adicionar Nova Pessoa")

    1.  Digite o **Nome**, **Sobrenome**, **Cargo** e **Endereço de email** de uma conta válida do Active Directory do Azure que deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Concluído**.

    >[AZURE.NOTE]O titular da conta do AD do Azure receberá um email que inclui um link de confirmação de conta para que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do 15Five ou as APIs fornecidas pelo 15Five para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao 15Five, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **15Five**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-15five-tutorial/IC784678.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-15five-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->