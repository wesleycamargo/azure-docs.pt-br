<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao RunMyProcess | Microsoft Azure" description="Saiba como usar o RunMyProcess com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o RunMyProcess
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528571).
  
O objetivo deste tutorial é mostrar a integração do Azure com o RunMyProcess. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do RunMyProcess
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao RunMyProcess poderão fazer logon único no aplicativo em seu site de empresa do RunMyProcess (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o RunMyProcess
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Cenário")
##Habilitando a integração de aplicativos com o RunMyProcess
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o RunMyProcess.

###Para habilitar a integração de aplicativos com o RunMyProcess, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **RunMyProcess**.

    ![Galeria de Aplicativos](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **RunMyProcess** e clique em **Concluir** para adicionar o aplicativo.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no RunMyProcess com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **RunMyProcess**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no RunMyProcess**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do RunMyProcess**, digite a URL usando o padrão "**http://company.runmyprocess.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no RunMyProcess**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configurar o logon único")

5.  Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do RunMyProcess como administrador.

6.  Vá para **Conta > Configuração**.

    ![Conta](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Conta")

7.  Clique na guia **Método de Autenticação**.

8.  Na seção **Método de Autenticação**, execute as seguintes etapas:

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  Como **Método**, selecione **SSO com Samlv2**.
    2.  No portal do Azure, na página de diálogo **Configurar logon único no RunMyProcess**, copie o valor de **URL de SAML SSO**, em seguida, cole-o na caixa de texto **Redirecionamento de SSO**.
    3.  No portal do Azure, na página de diálogo **Configurar logon único no RunMyProcess**, copie o valor de **URL de Serviço de Logoff Único**, em seguida, cole-o na caixa de texto **Redirecionamento de logoff**.
    4.  Na caixa de texto **Formato da Id de Nome **, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
    5.  Crie um arquivo **codificado em base 64** de seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**
    7.  Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no RunMyProcess, eles devem ser provisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **RunMyProcess** como administrador.

2.  Vá para **Conta > Usuários** e, em seguida, clique em **Novo Usuário**.

    ![Novo Usuário](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "Novo Usuário")

3.  Na seção **Configurações do Usuário**, execute as seguintes etapas:

    ![Perfil](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Perfil")

    1.  Digite o **Nome** e o **Email** de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.
    2.  Selecione uma **Linguagem IDE**, uma **Linguagem** e um **Perfil**.
    3.  Selecione**Enviar email de criação de conta para mim**.
    4.  Clique em **Salvar**.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do RunMyProcess ou as APIs fornecidas pelo RunMyProcess para provisionar as contas de usuário do Active Directory do Azure.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao RunMyProcess, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **RunMyProcess**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->