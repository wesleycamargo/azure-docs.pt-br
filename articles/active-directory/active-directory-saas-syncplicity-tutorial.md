<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao Syncplicity | Microsoft Azure" description="Saiba como usar o Syncplicity com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Syncplicity
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522417).
  
O objetivo deste tutorial é mostrar como configurar o logon único entre o AAD (Active Directory do Azure) e o Syncplicity.
  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Syncplicity
  
Depois de concluir este tutorial, os usuários do AAD aos quais você atribui acesso ao Syncplicity poderão fazer o logon único no aplicativo no site da sua empresa Syncplicity (serviço provedor iniciado pelo logon) ou usando o Introdução ao Painel de Acesso.

1.  Habilitando a integração de aplicativos para o Syncplicity
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Cenário")

##Habilitando a integração de aplicativos para o Syncplicity
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Syncplicity.

###Para habilitar a integração de aplicativos com o Syncplicity, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Syncplicity**.

    ![Galeria de aplicativos do Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Galeria de aplicativos do Syncplicity")

7.  No painel de resultados, selecione **Syncplicity** e clique em **Concluir** para adicionar o aplicativo.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##Configurando o logon único
  
Esta descreve como permitir que os usuários se autentiquem no Syncplicity com a respectiva conta do Active Directory do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Syncplicity**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Syncplicity**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de entrada do Syncplicity**, digite a URL que os usuários estão usando para entrar no seu aplicativo Syncplicity clique em **Avançar**.

    A URL do aplicativo é a URL do locatário do Syncplicity (por exemplo, **http://company.Syncplicity.com*):

    ![Configurar a URL do aplicativo](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Syncplicity**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configurar o logon único")

5.  Entre no locatário do **Syncplicity**.

6.  No menu na parte superior, clique em **admin**, selecione **configurações** e, em seguida, clique em **Domínio personalizado e logon único**.

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  Na página de caixa de diálogo **SSO (Logon único)**, execute as seguintes etapas:

    ![SSO (Logon único)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "SSO (Logon único)")

    1.  Na caixa de texto **Domínio personalizado**, digite o nome do seu domínio.
    2.  Selecione **Habilitado** como **Status do logon único**.
    3.  No portal do Microsoft Azure, na página **Configurar logon único no Syncplicity**, copie o valor de **ID de Entidade** e cole-o na caixa de texto **ID da entidade**.
    4.  No portal do Microsoft Azure, na página de diálogo **Configurar logon único no Syncplicity**, copie o valor da **URL do serviço de logon único** e, em seguida, cole-o na caixa de texto **URL da página de logon**.
    5.  No portal do Microsoft Azure, na página de diálogo **Configurar logon único no Syncplicity**, copie o valor da **URL do logoff remoto** e, em seguida, cole-o na caixa de texto **URL da página de logoff**.
    6.  Em **Certificado do provedor de identidade**, clique em **Escolher arquivo** e carregue o certificado que você baixou do portal do Microsoft Azure.
    7.  Clique em **Salvar Alterações**.

8.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Confirmação](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmação")

##Configurando o provisionamento de usuários
  
Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do AAD no Syncplicity.

###Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:

1.  Entre no locatário do **Syncplicity** (por exemplo, **https://company.Syncplicity.com*).

2.  Clique em **Admin** e selecione **contas de usuário**.

3.  Clique em **Adicionar um usuário**.

    ![Gerenciar usuários](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Gerenciar usuários")

4.  Digite o **Endereço de email** da conta do AAD que deseja provisionar, selecione **Usuário** como **Função** e clique em **Avançar**.

    ![Informações da conta](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Informações da conta")

    >[AZURE.NOTE]O titular da conta do AAD receberá um email incluindo um link para confirmar e ativar a conta.

5.  Selecione um grupo na sua empresa do qual o novo usuário deverá se tornar membro e clique em **Avançar**.

    ![Associação de grupo](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Associação de grupo")

    >[AZURE.NOTE]Se não houver nenhum grupo listado, apenas clique em **Avançar**.

6.  Selecione as pastas que você gostaria de colocar sob controle do Syncplicity no computador do usuário e clique em **Avançar**.

    ![Pastas do Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Pastas do Syncplicity")

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Syncplicity ou as APIs fornecidas pelo Syncplicity para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Syncplicity, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Syncplicity**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->