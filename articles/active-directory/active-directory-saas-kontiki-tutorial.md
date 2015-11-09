<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Kontiki | Microsoft Azure" 
    description="Saiba como usar o Kontiki com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure com o Kontiki
  
O objetivo deste tutorial é mostrar a integração do Azure com o Kontiki. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Kontiki com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Kontiki poderão fazer logon único no aplicativo em seu site de empresa do Kontiki (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Kontiki
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Cenário")
##Habilitando a integração de aplicativos para o Kontiki
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Kontiki.

###Para habilitar a integração de aplicativos com o Kontiki, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Kontiki**.

    ![Galeria de Aplicativos](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Kontiki** e clique em **Concluir** para adicionar o aplicativo.

    ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no Kontiki com a própria conta no AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Kontiki**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Kontiki**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Kontiki**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Kontiki (por exemplo: “*https://company.mc.eval.kontiki.com/*")) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar o logon único no Kontiki**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurar o logon único")

5.  Envie o metadatafile para a equipe de suporte do Kontiki.

    >[AZURE.NOTE]A configuração de logon único deve ser executada pela equipe de suporte do Kontiki. Assim que a configuração foi concluída, você receberá uma notificação.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurar o Logon Único")
##Configurando o provisionamento de usuários
  
Não há nenhum item de ação para a configuração de provisionamento de usuário para o Kontiki. Quando um usuário atribuído tenta fazer logon no Kontiki usando o painel de acesso, o Kontiki verifica se o usuário existe. Se não houver conta de usuário ainda, ela é criada automaticamente pelo Kontiki.
##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Kontiki, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Kontiki**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->