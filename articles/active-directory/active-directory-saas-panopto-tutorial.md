<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Panopto | Microsoft Azure" description="Saiba como usar o Panopto com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>

<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi"/>

#Tutorial: Integração do Active Directory do Azure com o Panopto
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524766).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Panopto. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Panopto
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Panopto poderão fazer logon único no aplicativo em seu site de empresa Panopto (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Panopto
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-panopto-tutorial/IC777665.png "Cenário")
##Habilitando a integração de aplicativos para Panopto
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Panopto.

###Para habilitar a integração de aplicativos para o Panopto, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-panopto-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-panopto-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-panopto-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Panopto**.

    ![Galeria de Aplicativos](./media/active-directory-saas-panopto-tutorial/IC777666.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Panopto** e clique em **Concluir** para adicionar o aplicativo.

    ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Panopto com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Panopto**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Panopto**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurar logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon no Panopto**, digite sua URL usando o seguinte padrão: "*https://\<nome-do-locatário>. Panopto.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Panopto**, para baixar seu certificado, clique em **Baixar certificado** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar logon único](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurar o logon único")

5.  Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Panopto como administrador.

6.  Na barra de ferramentas à esquerda, clique em **System** e, em seguida, clique em **Provedores de Identidade**.

    ![System](./media/active-directory-saas-panopto-tutorial/IC777670.png "System")

7.  Clique em **Adicionar Provedor**.

    ![Provedores de Identidade](./media/active-directory-saas-panopto-tutorial/IC777671.png "Provedores de Identidade")

8.  Na seção de provedor SAML, execute as seguintes etapas:

    ![Configuração do SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuração do SaaS")

    1.  Na lista **Tipo de Provedor**, selecione **SAML20**
    2.  Na caixa de texto **Nome da Instância**, digite um nome para a instância.
    3.  Na caixa de texto **Descrição Amigável**, digite uma descrição amigável.
    4.  No portal do Azure, na página de diálogo **Configurar logon único no Panopto**, copie o valor de **URL do emissor** e, em seguida, cole-a na caixa de texto **Emissor**.
    5.  No portal do Azure, na página de diálogo **Configurar logon único no Panopto**, copie o valor de **URL de SAML SSO** e, em seguida, cole-a na caixa de texto **Url de Página de Devolução**.
    6.  Crie um arquivo **codificado em base 64** de seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    7.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele em sua área de transferência e, então, cole-o na caixa de texto **PublicKey**
    8.  Clique em **Salvar**. ![Salvar](./media/active-directory-saas-panopto-tutorial/IC777673.png "Salvar")

9.  No portal do AD do Azure, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Panopto. Quando um usuário atribuído tenta fazer logon no Panopto usando o painel de acesso, o Panopto verifica se o usuário existe. Se ainda não houver nenhuma conta de usuário, ela será criada automaticamente pelo Panopto.

>[AZURE.NOTE]Você pode usar quaisquer outras ferramentas de criação de contas de usuários do Panopto ou APIs fornecidas pela Panopto para provisionar contas de usuário do AD do Azure.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Panopto, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos **Panopto**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-panopto-tutorial/IC777675.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-panopto-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=September15_HO1-->