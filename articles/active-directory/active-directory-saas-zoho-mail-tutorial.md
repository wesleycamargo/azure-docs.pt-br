<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Zoho Mail | Microsoft Azure" 
    description="Saiba como usar o Zoho Mail com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais." 
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

#Tutorial: Integração do Active Directory do Azure com o Zoho Mail
  
O objetivo deste tutorial é mostrar a integração do Azure com o Zoho Mail. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Zoho Mail
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Zoho Mail poderão fazer logon único no aplicativo em seu site de empresa do Zoho Mail (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração do aplicativo com o Zoho Mail
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Cenário")

##Habilitando a integração do aplicativo com o Zoho Mail
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Zoho Mail.

###Para habilitar a integração de aplicativos com o Zoho Mail, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Zoho Mail**.

    ![Galeria de Aplicativos](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Zoho Mail** e clique em **Concluir** para adicionar o aplicativo.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zoho Mail com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Zoho Mail**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Zoho Mail**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada no Zoho Mail**, digite a URL usando o padrão "**http://company.ZohoMail.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Zoho Mail**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoho Mail como administrador.

6.  Vá para o **Painel de Controle**.

    ![Painel de Controle](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Painel de Controle")

7.  Clique na guia **Autenticação SAML**.

    ![Autenticação do SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticação do SAML")

8.  Na seção **Detalhes da Autenticação SAML**, realize as seguintes etapas:

    ![Detalhes da Autenticação do SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Detalhes da Autenticação do SAML")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Zoho Mail**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Zoho Mail**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Zoho Mail**, copie o valor da **URL de Alteração de Senha** e cole-o na caixa de texto **URL de Alteração de Senha**.
    4.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **PublicKey**.
    6.  Para **Algoritmo**, selecione **RSA**.
    7.  Clique em **OK**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Zoho Mail, eles devem ser provisionados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Zoho Mail** como administrador.

2.  Vá para **Painel de Controle > Emails e Documentos**.

3.  Vá para **Detalhes do Usuário > Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Adicionar usuário")

4.  No diálogo **Adicionar usuários**, realize as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Adicionar usuário")

    1.  Digite o **Nome**, **Sobrenome**, **ID do Email** e **Senha** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **OK**.  

        >[AZURE.NOTE]O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Zoho Mail ou APIs fornecidas pelo Zoho Mail para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

###Para atribuir usuários ao Zoho Mail, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Zoho Mail**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->