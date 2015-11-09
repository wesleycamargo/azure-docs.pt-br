<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Adobe EchoSign | Microsoft Azure" 
    description="Saiba como usar o Adobe EchoSign com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" authors="markusvi"  
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

#Tutorial: Integração do Active Directory do Azure ao Adobe EchoSign

O objetivo deste tutorial é mostrar a integração do Azure ao Adobe EchoSign. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Adobe EchoSign

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Adobe EchoSign poderão fazer logon único no aplicativo em seu site de empresa do Adobe EchoSign (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Adobe EchoSign
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Cenário")
##Habilitando a integração de aplicativos para o Adobe EchoSign

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Adobe EchoSign.

###Para habilitar a integração de aplicativos ao Adobe EchoSign, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Adobe EchoSign**.

    ![Galeria de Aplicativos](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Adobe EchoSign** e clique em **Concluir** para adicionar o aplicativo.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Adobe EchoSign com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Adobe EchoSign**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Adobe EchoSign**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Adobe EchoSign**, digite a URL usando o padrão "**https://company.echosign.com/*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Adobe EchoSign**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Adobe EchoSign como administrador.

6.  No menu na parte superior, clique em **Conta** e, no painel de navegação da parte esquerda, clique em **Configurações do SAML**, em **Configurações da Conta**.

    ![Conta](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Conta")

7.  Na seção Configuração do SAML, execute as seguintes etapas:

    ![Configurações do SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Configurações do SAML")

    1.  Para **Modo do SAML**, selecione **SAML Obrigatório**.
    2.  Selecione **Permitir que os Administradores da Conta do EchoSign façam logon usando suas Credenciais do EchoSign**.
    3.  Para **Criação de Usuário**, selecione **Adicionar automaticamente os usuários autenticados por meio do SAML**.

8.  Siga em frente executando as seguintes etapas:

    ![Configurações do SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Configurações do SAML")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Adobe EchoSign**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IdP**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Adobe EchoSign**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IdP**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Adobe EchoSign**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do IdP**.
    4.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

		>[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    5.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do IdP**
    6.  Clique em **Salvar Alterações**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Adobe EchoSign, eles devem ser provisionados no Adobe EchoSign. No caso do Adobe EchoSign, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Adobe EchoSign** como administrador.

2.  No menu na parte superior, clique em **Conta** e, no painel de navegação da parte esquerda, clique em **Usuários e Grupos** e em **Criar um novo usuário**.

    ![Conta](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Conta")

3.  Na seção **Criar Novo Usuário**, realize as seguintes etapas:

    ![Criar Usuário](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Criar Usuário")

    1.  Digite o **Endereço de Email**, **Nome** e **Sobrenome** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Criar Usuário**.

		>[AZURE.NOTE]O titular da conta do Active Directory do Azure receberá um email que inclui um link de confirmação de conta para que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Adobe EchoSign ou as APIs fornecidas pelo Adobe EchoSign para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Adobe EchoSign, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Adobe EchoSign**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->