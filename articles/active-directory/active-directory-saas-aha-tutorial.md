<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Aha! | Microsoft Azure" 
    description="Saiba como usar o Aha! com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Aha!

O objetivo deste tutorial é mostrar a integração do Azure ao Aha! O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Aha!

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Aha! poderão fazer logon único no aplicativo em seu site de empresa do Aha! (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Aha!
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-aha-tutorial/IC798944.png "Cenário")
##Habilitando a integração de aplicativos para o Aha!

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Aha!.

###Para habilitar a integração de aplicativos para o Aha!, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-aha-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-aha-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-aha-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Aha!**.

    ![Galeria de Aplicativos](./media/active-directory-saas-aha-tutorial/IC798945.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Aha!** e clique em **Concluir** para adicionar o aplicativo.

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Aha! com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Aha!**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-aha-tutorial/IC798946.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Aha!**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-aha-tutorial/IC798947.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Aha!**, digite a URL usada pelos usuários para entrar no aplicativo Aha! (por exemplo: "**https://company.aha.io/session/new*")) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-aha-tutorial/IC798948.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Aha!**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-aha-tutorial/IC798949.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Aha! como um administrador.

6.  No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-aha-tutorial/IC798950.png "Configurações")

7.  Clique em **Conta**.

    ![Perfil](./media/active-directory-saas-aha-tutorial/IC798951.png "Perfil")

8.  Clique em **Segurança e logon único**.

    ![Segurança e logon único](./media/active-directory-saas-aha-tutorial/IC798952.png "Segurança e logon único")

9.  Na seção **Logon Único**, como **Provedor de Identidade**, selecione **SAML2.0**.

    ![Segurança e logon único](./media/active-directory-saas-aha-tutorial/IC798953.png "Segurança e logon único")

10. Na página de configuração **Logon Único**, realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-aha-tutorial/IC798954.png "Logon Único")

    1.  Na caixa de texto **Nome**, digite um nome para a sua configuração.
    2.  Para **Configurar usando**, selecione **Arquivo de Metadados**.
    3.  Para carregar seu arquivo de metadados baixado, clique em **Procurar**.
    4.  Clique em **Atualizar**.

11. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-aha-tutorial/IC798955.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Aha!, eles devem ser provisionados no Aha!. No caso do Aha!, o provisionamento é uma tarefa automatizada. Não há nenhum item de ação para você.
  
Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Aha! ou as APIs fornecidas pelo Aha! para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Aha!, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Aha!**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-aha-tutorial/IC798956.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-aha-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->