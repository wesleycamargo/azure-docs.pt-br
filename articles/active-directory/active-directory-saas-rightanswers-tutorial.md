<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao RightAnswers | Microsoft Azure" 
    description="Saiba como usar o RightAnswers com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure ao RightAnswers
  
O objetivo deste tutorial é mostrar a integração do Azure com o RightAnswers. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do RightAnswers
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao RightAnswers poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o RightAnswers
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Cenário")
##Habilitando a integração de aplicativos com o RightAnswers
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o RightAnswers.

###Para habilitar a integração de aplicativos com o RightAnswers, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **RightAnswers**.

    ![Galeria de Aplicativos](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **RightAnswers** e clique em **Concluir** para adicionar o aplicativo.
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no RightAnswers com sua conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **RightAnswers**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no RightAnswers**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurar o logon único")

3.  Na página **Definir Configurações do Aplicativo**, na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do RightAnswers (por exemplo: **https://fortify.rightanswers.com/portal/ss/*)) e clique em **Avançar**.

    ![Definir configurações de aplicativo](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único no RightAnswers**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurar o logon único")

5.  Envie o arquivo de metadados baixado para a equipe de suporte do RightAnswers.

    >[AZURE.NOTE]A equipe de suporte do RightAnswers precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no RightAnswers, eles devem ser provisionados no RightAnswers. No caso do RightAnswers, o provisionamento é uma tarefa automatizada. Não há nenhum item de ação para você.
  
Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do RightAnswers ou as APIs fornecidas pelo RightAnswers para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao RightAnswers, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **RightAnswers**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->