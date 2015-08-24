<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Workday | Microsoft Azure" description="Saiba como usar o Workday com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Workday
>[AZURE.TIP]Para comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=330042).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Workday. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no Workday
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Workday
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Configurando o provisionamento de usuários

![Cenário](./media/active-directory-saas-workday-tutorial/IC782919.png "Cenário")

##Habilitando a integração de aplicativos para o Workday
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Salesforce.

###Para habilitar a integração de aplicativos para o Workday, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de Aplicativos**, clique em **Adicionar um Aplicativo** e, em seguida, clique em **Adicionar um aplicativo para minha organização usar**.

    ![O que você deseja fazer?](./media/active-directory-saas-workday-tutorial/IC700995.png "O que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Workday**.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  No painel de resultados, selecione **Workday** e clique em **Concluir** para adicionar o aplicativo.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Workday com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  Na página de integração do aplicativo **Workday**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único **.

    ![Configurar logon único](./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Workday**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar logon único")

3.  Na página de caixa de diálogo **Configurar URL do aplicativo**, execute as seguintes etapas e então clique em **Avançar**.

    ![Configurar a URL do Aplicativo  
](./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar a URL do Aplicativo
")

    1.  Na caixa de texto **URL de logon**, digite a URL usada pelos usuários para entrar no Workday (p. ex.:*https://impl.workday.com/\<locatário>/login- saml2.htmld*)
    2.  Na caixa de texto **URL de Resposta do Workday**, digite a URL de resposta do Workday (p. ex.: *https://impl.workday.com/\<locatário>/login- saml.htmld*).

4.  Na página **Configurar logon único no Workday**, para baixar seu certificado, clique em **Baixar certificado** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar logon único](./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Workday como administrador.

6.  Vá para **Menu > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Vá para **Administração de Conta**.

    ![Administração de Conta](./media/active-directory-saas-workday-tutorial/IC782924.png "Administração de Conta")

8.  Vá para **Editar Configuração de Locatário – Segurança**.

    ![Editar Segurança de Locatário](./media/active-directory-saas-workday-tutorial/IC782925.png "Editar Segurança de Locatário")

9.  Na seção **URLs de Redirecionamento**, execute as seguintes etapas:

    ![URLs de Redirecionamento](./media/active-directory-saas-workday-tutorial/IC782958.png "URLs de Redirecionamento")

    1.  Clique em **Adicionar Linha**.
    2.  Na caixa de texto **URL de Redirecionamento de Logon** e na caixa de texto **URL de Redirecionamento Móvel**, digite a **URL do Locatário do Workday** que você inseriu na página **Configurar a URL do Aplicativo** do portal do Azure.
    3.  Na caixa de texto **Ambiente**, digite o nome do ambiente.  

        >[AZURE.NOTE]O valor do atributo Ambiente é vinculado ao valor da URL do locatário:
		>
        >-   Se o nome da URL de domínio do locatário do Workday começa com impl (p. ex.:*https://impl.workday.com/\<locatário>/login- saml2.htmld*), o atributo **Ambiente** deve ser definido como Implementação.
        >-   Se o nome de domínio começar de outra forma, será preciso entrar em contato com o Workday para obter um valor de **Ambiente** correspondente.

10. Na seção **Configuração do SAML**, execute as seguintes etapas:

    ![Configuração do SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuração do SAML")

    1.  Selecione **Habilitar a Autenticação do SAML**.
    2.  Clique em **Adicionar Linha**.

11. Na seção Provedores de Identidade do SAML, execute as seguintes etapas:

    ![Provedores de Identidade do SAML](./media/active-directory-saas-workday-tutorial/IC782927.png "Provedores de Identidade do SAML")

    1.  Na caixa de texto Nome do Provedor de Identidade, digite um nome de provedor (p. ex.:*SPInitiatedSSO*).
    2.  No portal do Azure, na página de diálogo **Configurar logon único no Workday**, copie o valor da **ID do Provedor de Identidade** e, em seguida, cole-o na caixa de texto **Emissor**.
    3.  Clique em **Certificado de Chave Pública do Provedor de Identidade** e, em seguida, clique em **Criar**. ![Criar](./media/active-directory-saas-workday-tutorial/IC782928.png "Criar")
    4.  Clique em **Criar Chave Pública x509**. ![Criar](./media/active-directory-saas-workday-tutorial/IC782929.png "Criar")
    5.  Na seção **Visualizar Chave Pública x509**, execute as seguintes etapas: ![Visualizar Chave Pública x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Visualizar Chave Pública x509")
        1.  Na caixa de texto **Nome**, digite um nome para o seu certificado (por ex.: *PPE\_SP*).
        2.  Na caixa de texto **Válido a partir de **, digite o valor do atributo “válido a partir de” do seu certificado.
        3.  Na caixa de texto **Válido até**, digite o valor do atributo “válido até” do seu certificado.
		
            >[AZURE.NOTE]Você pode obter as datas de “válido a partir de” e “válido até” do certificado baixado clicando duas vezes nele. As datas são listadas na guia **Detalhes**.

        4.  Crie um arquivo **Codificado em base 64** a partir do seu certificado baixado.

			>[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

        5.  Abra seu certificado codificado em base 64 no bloco de notas e copie o conteúdo dele.
        6.  Na caixa de texto **Certificado**, cole o conteúdo da área de transferência.
        7.  Clique em **OK**.

    6.  Execute as seguintes etapas: ![Configuração do SSO](./media/active-directory-saas-workday-tutorial/IC792131.png "Configuração do SSO")
        1.  Na caixa de texto **ID do Provedor de Serviço**, digite ****http://www.workday.com**.
2.  Selecione **Habilitar a Autenticação do SAML Iniciada por SP**.
        3.  No portal do Azure, na página de diálogo **Configurar logon único no Workday**, copie o valor da **URL do Serviço de Logon Único** e, em seguida, cole-o na caixa de texto **URL do Serviço SSO IdP**.
        4.  Selecione **Não Esvazie a Solicitação de Autenticação iniciada por SP**.

    7.  Execute as seguintes etapas: ![Método de Assinatura de Solicitação de Autenticação](./media/active-directory-saas-workday-tutorial/IC782932.png "Método de Assinatura de Solicitação de Autenticação")
        1.  Para **Solicitação de Método de Autenticação de Assinatura **, selecione **SHA256**.

    8.  Clique em **OK**. ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. No portal do AD do Azure, na página **Configurar logon único no Workday**, clique em **Concluir** para fechar a caixa de diálogo.

    ![Configurar logon único](./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar logon único")

##Configurando o provisionamento de usuários
  
Para obter um usuário de teste provisionado no Workday, você precisa entrar em contato com a equipe de suporte do Workday. A equipe de suporte do Workday criará o usuário para você.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao Workday, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Workday **, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-workday-tutorial/IC782935.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-workday-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->