<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao SumoLogic | Microsoft Azure" description="Saiba como usar o SumoLogic com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o SumoLogic
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=525318).
  
O objetivo deste tutorial é mostrar a integração do Azure com o SumoLogic. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do SumoLogic
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao SumoLogic poderão fazer logon único no aplicativo em seu site de empresa SumoLogic (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o SumoLogic
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Cenário")

##Habilitando a integração de aplicativos para o SumoLogic
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SumoLogic.

###Para habilitar a integração de aplicativos com o SumoLogic, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **sumologic**.

    ![Galeria de aplicativos](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **SumoLogic** e clique em **Concluir** para adicionar o aplicativo.

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SumoLogic com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário carregar um certificado codificado em base 64 no locatário do SumoLogic. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **SumoLogic**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no SumoLogic**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do SumoLogic**, digite sua URL usando o seguinte padrão "*https://\<nome do locatário>.SumoLogic.com*" e clique em **Avançar**.

    ![Configurar URL aoo](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurar URL aoo")

4.  Na página **Configurar logon único no SumoLogic**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa SumoLogic como um administrador.

6.  Vá para **Gerenciar > Segurança**.

    ![Gerenciar](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gerenciar")

7.  Clique em **SAML**.

    ![Configurações de globais de segurança](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Configurações de globais de segurança")

8.  Na lista **Selecionar uma configuração ou criar uma nova**, selecione **Azure AD** e clique em **Configurar**.

    ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurar SAML 2.0")

9.  Na página **Configurar SAML 2.0**, execute as seguintes etapas:

    ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurar SAML 2.0")

    1.  Na caixa de texto **Nome da Configuração**, digite **Azure AD**.
    2.  Selecione **Modo de Depuração**.
    3.  No portal do Azure, na página de diálogo **Configurar logon único no SumoLogic**, copie o valor da **URL do emissor** e, em seguida, cole-o na caixa de texto **Emissor**.
    4.  No portal do Azure, na página de diálogo **Configurar logon único no SumoLogic**, copie o valor da **URL da solicitação de autenticação** e cole-o na caixa de texto **URL da solicitação de autenticação**.
    5.  Crie um arquivo **Codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509**.
    7.  Como **Atributo de Email**, selecione **Usar SAML subject**.
    8.  Selecione **Configuração de logon iniciada por SP**.
    9.  Na caixa de texto **Caminho de logon**, digite **Azure**.
    10. Clique em **Salvar**.

10. No portal do Azure, na página **Configurar logon único no SumoLogic**, selecione a confirmação de configuração de logon único e clique em **Concluir**.

    ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SumoLogic, eles deverão ser provisionados no SumoLogic. No caso do SumoLogic, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon no seu locatário do **SumoLogic**.

2.  Vá para **Gerenciar > Usuários**.

    ![Usuários](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Usuários")

3.  Clique em **Adicionar**.

    ![Usuários](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Usuários")

4.  Na caixa de diálogo **Novo Usuário**, execute as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-sumologic-tutorial/IC778563.png "Novo Usuário")

    1.  Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **Nome**, **Sobrenome** e **Email**.
    2.  Selecione uma função.
    3.  Como **Status**, selecione **Ativo**.
    4.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do SumoLogic ou as APIs fornecidas pelo SumoLogic para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao SumoLogic, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **SumoLogic**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->