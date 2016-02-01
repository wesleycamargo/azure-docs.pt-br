<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Druva | Microsoft Azure" 
    description="Saiba como usar o Druva com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao Druva

O objetivo deste tutorial é mostrar a integração do Azure ao Druva. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Druva

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Druva poderão fazer logon único no aplicativo em seu site de empresa do Druva (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Druva
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-druva-tutorial/IC795084.png "Cenário")
##Habilitando a integração de aplicativos para Druva

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Druva.

###Para habilitar a integração de aplicativos para o Druva, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-druva-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-druva-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-druva-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Druva**.

    ![Galeria de Aplicativos](./media/active-directory-saas-druva-tutorial/IC795085.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Druva** e clique em **Concluir** para adicionar o aplicativo.

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Druva com a própria conta no Azure AD usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base-64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Seu aplicativo Druva espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso.

![Atributos do token SAML](./media/active-directory-saas-druva-tutorial/IC795087.png "Atributos do token SAML")

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Active Directory do Azure, na página de integração de aplicativos do **Druva**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-druva-tutorial/IC795027.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Druva**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-druva-tutorial/IC795088.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Druva**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Druva (por exemplo: "**https://cloud.druva.com/home/*”)) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-druva-tutorial/IC795089.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Druva**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-druva-tutorial/IC795090.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do Druva como administrador.

6.  Vá para **Gerenciar > Configurações**.

    ![Configurações](./media/active-directory-saas-druva-tutorial/IC795091.png "Configurações")

7.  Na caixa de diálogo Configurações de Logon Único, execute as seguintes etapas:

    ![Configurações de Logon Único](./media/active-directory-saas-druva-tutorial/IC795092.png "Configurações de Logon Único")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Druva**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Druva**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do Provedor de Identidade**.
    3.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de ID**
    5.  Para abrir a página **Configurações**, clique em **Salvar**.

8.  Na página **Configurações**, clique em **Gerar Token de SSO**.

    ![Configurações](./media/active-directory-saas-druva-tutorial/IC795093.png "Configurações")

9.  No diálogo **Token de Autenticação de Logon Único**, realize as seguintes etapas:

    ![Token SSO](./media/active-directory-saas-druva-tutorial/IC795094.png "Token SSO")

    1.  Clique em **Copiar**.
    2.  Clique em **fechar**

10. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-druva-tutorial/IC795095.png "Configurar o logon único")

11. Na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-druva-tutorial/IC795096.png "Atributos")

12. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

	|Nome do atributo|Valor do atributo|
    |---|---|
    |insync\_auth\_token|<*valor da área de transferência*>|

    1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para a linha.
    3.  Na caixa de texto **Valor do Atributo**, digite o valor do atributo mostrado para essa linha.
    4.  Clique em **Concluído**.

13. Clique em **Aplicar alterações**.
##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Druva, eles devem ser provisionados no Druva. No caso do Druva, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Druva** como administrador.

2.  Vá para **Gerenciar > Usuários**.

    ![Gerenciar Usuários](./media/active-directory-saas-druva-tutorial/IC795097.png "Gerenciar Usuários")

3.  Clique em **Criar Novo**.

    ![Gerenciar Usuários](./media/active-directory-saas-druva-tutorial/IC795098.png "Gerenciar Usuários")

4.  Na caixa de diálogo Criar Novo Usuário, execute as seguintes etapas:

    ![Criar Novo Usuário](./media/active-directory-saas-druva-tutorial/IC795099.png "Criar Novo Usuário")

    1.  Digite o endereço de email e o nome da conta de usuário válida do Active Directory do Azure, que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Criar Usuário**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Druva ou as APIs fornecidas pelo Druva para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Druva, execute as etapas a seguir:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Druva**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-druva-tutorial/IC795100.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-druva-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->