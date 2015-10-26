<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Lucidchart | Microsoft Azure" description="Saiba como usar o Lucidchart com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Lucidchart
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=532346).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Lucidchart. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Lucidchart com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Lucidchart poderão fazer um logon único no aplicativo do site da sua empresa Lucidchart (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Lucidchart
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Cenário")
##Habilitando a integração de aplicativos com o Lucidchart
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Lucidchart.

###Para habilitar a integração de aplicativos com o Lucidchart, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Lucidchart**.

    ![Galeria de Aplicativos](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Lucidchart** e clique em **Concluir** para adicionar o aplicativo.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Lucidchart com sua conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **Lucidchart**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Lucidchart**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurar o logon único")

3.  Na página **Configurar URL do aplicativo**, na caixa de texto **URL de logon do Lucidchart**, digite a URL usada pelos seus usuários para fazer logon no seu aplicativo Lucidchart (p. ex.: “**https://chart2.office.lucidchart.com/saml/sso/azure*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Lucidchart**, para baixar seus metadados, clique em **Baixar metadados** e, em seguida, salve o arquivo de dados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Lucidchart como administrador.

6.  No menu na parte superior, clique em **Equipe**.

    ![Equipe](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Equipe")

7.  Clique em **Aplicativo > Gerenciar SAML**.

    ![Gerenciar SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gerenciar SAML")

8.  Na página de diálogo **Configurações de autenticação SAML**, execute as seguintes etapas:

    1.  Selecione **Habilitar autenticação SAML** e, em seguida, clique em **Opcional**.![Configurações da autenticação SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Configurações da autenticação SAML")
    2.  Na caixa de texto **Domínio**, digite seu domínio e, em seguida, clique em **Alterar certificado**.![Alterar certificado](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Alterar certificado")
    3.  Abra o arquivo de metadados baixado, copie o conteúdo e, em seguida, cole-o na caixa de texto **Carregar metadados**.![Carregar metadados](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Carregar metadados")
    4.  Selecione **Adicionar automaticamente novo usuário à equipe** e, em seguida, clique em **Salvar alterações**.![Salvar alterações](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Salvar Alterações")

9.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Lucidchart. Quando um usuário atribuído tenta fazer logon no Lucidchart usando o painel de acesso, o Lucidchart verifica se o usuário existe. Se não houver conta de usuário ainda, ela é criada automaticamente pelo Lucidchart.
##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Lucidchart, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Lucidchart**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->