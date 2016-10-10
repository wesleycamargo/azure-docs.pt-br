<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Rally Software | Microsoft Azure" 
    description="Saiba como usar o Rally Software com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Rally Software
  
O objetivo deste tutorial é mostrar a integração do Azure com o Rally Software. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Rally Software
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Rally Software
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Cenário")
##Habilitando a integração de aplicativos com o Rally Software
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Rally Software.

###Para habilitar a integração de aplicativos com o Rally Software, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **rally**.

    ![Galeria de aplicativos](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Rally Software** e clique em **Concluir** para adicionar o aplicativo.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no Rally Software com a própria conta no AD do Azure usando federação baseada no protocolo SAML. Como parte desse procedimento, é necessário carregar um certificado para o Rally Software.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Rally Software**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Rally**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Locatário do Rally Software**, digite a URL usando o padrão "*https://\<nome-locatário>.rally.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar o logon único no Rally**, clique em Baixar metadados e salve-o no computador.

    ![Configurar o logon único](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar o logon único")

5.  Faça logon em seu locatário do **Rally Software**.

6.  Na barra de ferramentas na parte superior, clique em **Instalação** e selecione **Assinatura**.

    ![Assinatura](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Assinatura")

7.  Clique no botão **Ação** na barra de ferramentas na parte superior à direita e selecione **Editar Assinatura**.

8.  Na página do diálogo **Assinatura**, realize as seguintes etapas e clique em **Salvar e Fechar**:

    ![Autenticação](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticação")

    1.  Selecione **Autenticação do Rally ou SSO** na lista suspensa Autenticação
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Rally Software**, copie o valor da **ID do Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.
    3.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Rally Software**, copie o valor da **URL de Logoff Remoto**.

9.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para usuários do AAD conseguirem efetuar logon, eles devem ser provisionados para o aplicativo Rally Software usando seus nomes de usuário do Active Directory do Azure.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon no seu locatário do Rally Software.

2.  Vá para **Instalação > USUÁRIOS** e clique em **+ Adicionar Novo**.

    ![Usuários](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Usuários")

3.  Digite o nome na caixa de texto Novo Usuário e clique em **Adicionar com Detalhes**.

4.  Na seção **Criar Usuário**, realize as seguintes etapas:

    ![Criar Usuário](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Criar Usuário")

    1.  Na caixa de texto **Nome do Usuário**, digite o nome do usuário do AD do Azure que você deseja provisionar.
    2.  Na caixa de texto **Endereço de Email**, digite o endereço de email do usuário do Azure AD que você deseja provisionar.
    3.  Clique em **Salvar e Fechar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Rally Software ou as APIs fornecidas pelo Rally Software para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Rally Software, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Rally Software**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0928_2016-->