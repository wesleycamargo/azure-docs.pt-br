<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Aba Sainsburys Connect | Microsoft Azure" 
    description="Saiba como usar o Aba Sainsburys Connect com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Aba Sainsburys Connect

O objetivo deste tutorial é mostrar a integração do Azure ao Aba Sainsburys Connect. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Aba Sainsburys Connect

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Aba Sainsburys Connect poderão fazer logon único no aplicativo em seu site de empresa do Aba Sainsburys Connect (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Aba Sainsburys Connect
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Cenário")
##Habilitando a integração de aplicativos para o Aba Sainsburys Connect

O objetivo desta seção é descrever como habilitar a integração de aplicativos ao Aba Sainsburys Connect.

###Para habilitar a integração de aplicativos ao Aba Sainsburys Connect, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Aba Sainsburys Connect**.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "Aba Sainsburys Connect")

7.  No painel de resultados, selecione **Aba Sainsburys Connect** e clique em **Concluir** para adicionar o aplicativo.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "Aba Sainsburys Connect")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Aba Sainsburys Connect com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Aba Sainsburys Connect**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Aba Sainsburys Connect**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Configurar o logon único")

3.  Na página **Definir Configurações do Aplicativo**, execute as seguintes etapas:

    ![Definir configurações de aplicativo](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Definir configurações de aplicativo")

    1.  Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Aba Sainsburys Connect (por exemplo: **https://myaba.co.uk/client-access/sainsburys/saml.php*)).
2.  Clique em **Próximo**.

4.  Na página **Configurar logon único no Aba Sainsburys Connect**, para baixar seus metadados, clique em **Baixar metadados** e salve o arquivo de metadados no computador.

    ![Configurar o logon único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Configurar o logon único")

5.  Envie o arquivo de metadados baixado à equipe de suporte do Aba Sainsburys Connect.

    >[AZURE.NOTE]A equipe de suporte do Aba Sainsburys Connect precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Aba Sainsburys Connect, eles devem ser provisionados no Aba Sainsburys Connect. No caso do Aba Sainsburys Connect, as contas do usuário precisam ser criadas pela equipe de suporte do Aba Sainsburys Connect.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do Aba Sainsburys Connect ou as APIs fornecidas pelo Aba Sainsburys Connect para provisionar as contas de usuário do Active Directory do Azure.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Aba Sainsburys Connect, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Aba Sainsburys Connect**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->