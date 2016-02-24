<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao SimpleNexus | Microsoft Azure" 
    description="Saiba como usar o SimpleNexus com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure com o SimpleNexus
  
O objetivo deste tutorial é mostrar a integração do Azure com o SimpleNexus. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do SimpleNexus
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SimpleNexus poderão fazer logon único no aplicativo em seu site de empresa do SimpleNexus (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o SimpleNexus
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Cenário")
##Habilitando a integração de aplicativos para o SimpleNexus
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o SimpleNexus.

###Para habilitar a integração de aplicativos com o SimpleNexus, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Simple Nexus**.

    ![Galeria de Aplicativos](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **SimpleNexus** e clique em **Concluir** para adicionar o aplicativo.

    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SimpleNexus com sua conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **SimpleNexus**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no SimpleNexus**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do SimpleNexus**, digite a URL usando o padrão "**https://simplenexus.com/CompanyName\_login*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no SimpleNexus**, clique em **Baixar metadados** e encaminhe o arquivo de metadados para a equipe de suporte do SimpleNexus.

    ![Configurar o logon único](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurar o logon único")

    >[AZURE.NOTE]O logon único precisa ser habilitado pela equipe de suporte do SimpleNexus.

5.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SimpleNexus, eles deverão ser provisionados no SimpleNexus. No caso do SimpleNexus, o provisionamento será uma tarefa manual executada pelo administrador do locatário.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do SimpleNexus ou as APIs fornecidas pelo SimpleNexus para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao SimpleNexus, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **SimpleNexus**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->