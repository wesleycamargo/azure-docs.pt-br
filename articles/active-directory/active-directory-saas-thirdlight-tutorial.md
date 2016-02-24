<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Thirdlight | Microsoft Azure" 
    description="Saiba como usar o Thirdlight com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Thirdlight
  
O objetivo deste tutorial é mostrar a integração do Azure ao Thirdlight. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Thirdlight com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Thirdlight poderão fazer logon único no aplicativo em seu site de empresa do Thirdlight (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Thirdlight
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Cenário")

##Habilitando a integração de aplicativos para o Thirdlight
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Thirdlight.

###Para habilitar a integração de aplicativos com o Thirdlight, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Thirdlight**.

    ![Galeria de Aplicativos](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Thirdlight** e clique em **Concluir** para adicionar o aplicativo.

    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Thirdlight com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o Thirdlight exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Thirdlight**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Thirdlight**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Thirdlight**, digite a URL usada pelos usuários para fazer logon em seu aplicativo Thirdlight (por exemplo: "**http://azuresso2.thirdlight.com/*")) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Thirdlight**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Thirdlight como um administrador.

6.  Vá para **Configuração > Administração do Sistema** e clique em **SAML2**.

    ![Administração do Sistema](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administração do Sistema")

7.  Na seção de configuração do SAML2, execute as seguintes etapas:

    ![Logon Único do SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "Logon Único do SAML")

    1.  Selecione **Habilitar Logon Único do SAML2**.
    2.  Para **Fonte de metadados do IdP**, selecione **Carregar metadados do IdP do XML**.
    3.  Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **XML de Metadados do IdP**.
    4.  Clique em **Salvar configurações do SAML2**.

8.  No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Thirdlight, eles devem ser provisionados no Thirdlight. No caso do Thirdlight, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Thirdlight** como administrador.

2.  Vá para a guia **Usuários**.

3.  Selecione **Usuários e Grupos**.

4.  Clique no botão **Adicionar novo Usuário**.

5.  Digite os valores de **Nome de usuário, Nome ou Descrição, Email, Escolher uma Predefinição ou Grupo de Novos Membros** de uma conta válida do AAD que você deseja provisionar.

6.  Clique em **Criar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Thirdlight ou as APIs fornecidas pelo Thirdlight para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Thirdlight, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Thirdlight**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->