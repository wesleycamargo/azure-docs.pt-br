<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao AppDynamics | Microsoft Azure" description="Saiba como usar o AppDynamics com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao AppDynamics
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529078).

O objetivo deste tutorial é mostrar a integração do Azure ao AppDynamics. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do AppDynamics

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao AppDynamics poderão fazer logon único no aplicativo em seu site de empresa AppDynamics (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração do aplicativo para o AppDynamics
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Cenário")
##Habilitando a integração do aplicativo para o AppDynamics

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AppDynamics.

###Para habilitar a integração de aplicativos para o AppDynamics, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **AppDynamics**.

    ![Galeria de Aplicativos](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **AppDynamics** e clique em **Concluir** para adicionar o aplicativo.

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AppDynamics com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **AppDynamics**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no AppDynamics**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do AppDynamics**, digite a URL usada pelos usuários para fazer logon no AppDynamics (por exemplo: ("**https://companyname.saas.appdynamics.com*") e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no AppDynamics**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurar o Logon Único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa AppDynamics como um administrador.

6.  Na barra de ferramentas, na parte superior, clique em **Configurações** e clique em **Administrador**.

    ![Administração](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administração")

7.  Clique na guia **Provedor de Autenticação**.

    ![Provedor de Autenticação](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Provedor de Autenticação")

8.  Na seção **Provedor de Autenticação**, execute as seguintes etapas:

    ![Configuração de SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuração de SAML")

    1.  Como **Provedor de Autenticação**, selecione **SAML**.
    2.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no AppDynamics**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no AppDynamics**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    4.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**
    6.  Clique em **Salvar**. ![Salvar](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Salvar")

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o Logon Único](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurar o Logon Único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no AppDynamics, eles devem ser provisionados no AppDynamics. No caso do AppDynamics, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa AppDynamics como um administrador.

2.  Vá para **Usuários** e clique em **+** para abrir a caixa de diálogo **Criar Usuário**.

    ![Usuários](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Usuários")

3.  Na seção **Criar Usuário**, execute as seguintes etapas:

    ![Criar Usuário](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Criar Usuário")

    1.  Digite **Nome de usuário**, **Nome**, **Email**, **Nova Senha**, **Repetir Nova Senha** de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do AppDynamics ou as APIs fornecidas pelo AppDynamics para provisionar as contas de usuário do AD do Azure.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao AppDynamics, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **AppDynamics**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->