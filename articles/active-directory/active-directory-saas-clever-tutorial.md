<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Clever | Microsoft Azure" 
    description="Saiba como usar o Clever com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: integração do Active Directory do Azure ao Clever

O objetivo deste tutorial é mostrar a integração do Azure ao Clever. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Clever

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Clever poderão fazer logon único no aplicativo em seu site de empresa do Clever (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Clever
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-clever-tutorial/IC798977.png "Cenário")
##Habilitando a integração de aplicativos para o Clever

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Clever.

###Para habilitar a integração de aplicativos para o Clever, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-clever-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-clever-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-clever-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Clever**.

    ![Galeria de Aplicativos](./media/active-directory-saas-clever-tutorial/IC798978.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Clever** e clique em **Concluir** para adicionar o aplicativo.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Clever com a própria conta no Azure AD usando federação baseada no protocolo SAML. Seu aplicativo Clever espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso.

![Atributos](./media/active-directory-saas-clever-tutorial/IC798980.png "Atributos")

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Clever**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-clever-tutorial/IC784682.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Clever**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-clever-tutorial/IC798981.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Clever**, digite a URL usada pelos seus usuários para fazer logon em seu aplicativo Clever (por exemplo: **https://clever.com/in/azsandbox*)) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-clever-tutorial/IC798982.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Clever**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-clever-tutorial/IC798983.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do Clever como um administrador.

6.  Na barra de ferramentas, clique em **Logon Instantâneo**.

    ![Logon Instantâneo](./media/active-directory-saas-clever-tutorial/IC798984.png "Logon Instantâneo")

7.  Na página **Logon Instantâneo**, realize as seguintes etapas:

    ![Logon Instantâneo](./media/active-directory-saas-clever-tutorial/IC798985.png "Logon Instantâneo")

    1.  Digite a **URL de Logon**.  

        >[AZURE.NOTE]A **URL de Logon** é um valor personalizado. Você pode obter o valor real com a equipe de suporte do Clever.

    2.  Para **Sistema de Identidade**, selecione **ADFS**.
    3.  Clique em **Salvar**.

8.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-clever-tutorial/IC798986.png "Configurar o logon único")

9.  Na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.

    ![Atributos](./media/active-directory-saas-clever-tutorial/IC795920.png "Atributos")

10. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    ![atributos de token saml](./media/active-directory-saas-clever-tutorial/IC795921.png "atributos de token saml")

	|Nome do atributo|Valor do atributo|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2.  Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para essa linha.
    3.  Na caixa de texto **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.
    4.  Clique em **Concluído**.

11. Clique em **Aplicar Alterações**.

##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Clever, eles devem ser provisionados no Clever. No caso do Clever, o provisionamento é uma tarefa manual que precisa ser realizada por sua equipe de suporte do Clever.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Clever ou as APIs fornecidas pelo Clever para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Clever, execute as etapas a seguir:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Clever**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-clever-tutorial/IC798987.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-clever-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->