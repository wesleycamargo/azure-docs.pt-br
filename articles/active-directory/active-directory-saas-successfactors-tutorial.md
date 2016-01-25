<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o SuccessFactors | Microsoft Azure"
    description="Saiba como usar o SuccessFactors com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure com o SuccessFactors
  
O objetivo deste tutorial é mostrar a integração do Azure com o SuccessFactors no **modo de logon único iniciado pelo SP**. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Assinatura habilitada por logon único do SuccessFactors no modo iniciado pelo Provedor de Serviços
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SuccessFactors poderão fazer logon único no aplicativo em seu site de empresa do SuccessFactors (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o SuccessFactors
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Cenário")

##Habilitando a integração de aplicativos com o SuccessFactors
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o SuccessFactors.

###Para habilitar a integração de aplicativos com o SuccessFactors, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **SuccessFactors**.

    ![Galeria de Aplicativos](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **SuccessFactors** e clique em **Concluir** para adicionar o aplicativo.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SuccessFactors com sua conta do AD do Azure usando federação baseada no protocolo SAML.
  
Para obter logon único configurado, você precisará entrar em contato com sua equipe de suporte do SuccessFactors.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **SuccessFactors**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no SuccessFactors**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Logon Único do SuccessFactors**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do SuccessFactors (por exemplo: "**https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*")).
2.  Na caixa de texto **URL de Resposta do SuccessFactors**, digite ****https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**.

        >[AZURE.NOTE]Esse valor é apenas um espaço reservado temporário. Você obterá o valor real com a equipe de suporte do SuccessFactors. Mais tarde neste tutorial, você encontrará instruções sobre como entrar em contato com a equipe de suporte do SuccessFactors. No contexto desta conversa, você receberá a URL de Resposta do SuccessFactors real.

4.  Na página **Configurar logon único no SuccessFactors**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configurar o logon único")

5.  Para configurar o logon único baseado no SAML, entre em contato com a equipe de suporte do SuccessFactors e forneça a eles os seguintes itens:

    1.  O certificado baixado
    2.  A URL de Logon Remoto
    3.  A URL de Logoff Remoto

    >[AZURE.IMPORTANT]Solicite que a equipe de suporte do SuccessFactors defina o parâmetro NameId Format como “*Unspecified*”.

    A equipe de suporte do Successfactors enviará a **URL de Resposta do Successfactors** correta necessária para o diálogo **Configurar URL do Aplicativo**.

6.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no SuccessFactors, eles deverão ser provisionados no SuccessFactors. No caso do SuccessFactors, o provisionamento será uma tarefa manual.
  
Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a equipe de suporte do SuccessFactors.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao SuccessFactors, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **SuccessFactors**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->