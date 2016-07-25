<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao NetDocuments | Microsoft Azure" 
    description="Saiba como usar o NetDocuments com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o NetDocuments
  
O objetivo deste tutorial é mostrar a integração do Azure com o NetDocuments. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do NetDocuments
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao NetDocuments poderão fazer logon único no aplicativo em seu site de empresa do NetDocuments (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o NetDocuments
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Cenário")
##Habilitando a integração de aplicativos com o NetDocuments
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o NetDocuments.

###Para habilitar a integração de aplicativos com o NetDocuments, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **NetDocuments**.

    ![Galeria de Aplicativos](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **NetDocuments** e clique em **Concluir** para adicionar o aplicativo.

    ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no NetDocuments com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o NetDocuments exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **NetDocuments**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no NetDocuments**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para entrar no aplicativo NetDocuments (por exemplo: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Na caixa de texto **URL de Resposta do NetDocuments**, digite o mesmo valor que você digitou na caixa de texto **URL de Logon**.

        >[AZURE.NOTE]Você pode encontrar o valor correto no fim do diálogo **Identidade Federada** (veja a captura de tela da etapa 9).

    3.  Clique em **Próximo**.

4.  Na página **Configurar logon único no NetDocuments**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurar o logon único")

5.  Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do NetDocuments como administrador.

6.  Vá para **Administrador**.

7.  Clique em **Adicionar e remover usuários e grupos**.

    ![Repositório](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

8.  Clique em **Configurar opções de autenticação avançadas**.

    ![Configurar opções de autenticação avançada](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurar opções de autenticação avançada")

9.  Na caixa de diálogo **Identidade Federada**, execute as seguintes etapas:

    ![Identidade Federada](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Identidade Federada")

    1.  Para **Tipo de servidor de identidade federada**, selecione **Serviços de Federação do Active Directory**.
    2.  Clique em **Escolher arquivo** para carregar o arquivo de metadados baixado.
    3.  Clique em **OK**.

10. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no NetDocuments, eles devem ser provisionados no NetDocuments. No caso do NetDocuments, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **NetDocuments** como administrador.

2.  No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administrador")

3.  Clique em **Adicionar e remover usuários e grupos**.

    ![Repositório](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

4.  Na caixa de texto **Endereço de Email**, digite o endereço de email de uma conta válida do Active Directory do Azure que você deseja provisionar e clique em **Adicionar Usuário**.

    ![Endereço de Email](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Endereço de Email")

    >[AZURE.NOTE]O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do NetDocuments ou as APIs fornecidas pelo NetDocuments para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao NetDocuments, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **NetDocuments**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->