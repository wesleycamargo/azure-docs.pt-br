<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o InsideView | Microsoft Azure" 
    description="Saiba como usar o InsideView com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure com o InsideView
  
O objetivo deste tutorial é mostrar a integração do Azure com o InsideView. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do InsideView
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao InsideView poderão fazer logon único no aplicativo em seu site de empresa do InsideView (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o InsideView
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-insideview-tutorial/IC794128.png "Cenário")
##Habilitando a integração de aplicativos com o InsideView
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o InsideView.

###Para habilitar a integração de aplicativos com o InsideView, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-insideview-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-insideview-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-insideview-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **InsideView**.

    ![Galeria de Aplicativos](./media/active-directory-saas-insideview-tutorial/IC794129.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **InsideView** e clique em **Concluir** para adicionar o aplicativo.

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no InsideView com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **InsideView**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no InsideView**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Resposta do InsideView**, digite a URL de SSO do InsideView (por exemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no InsideView**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do InsideView como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Administrador**, **Configurações de Logon Único** e em **Adicionar SAML**.

    ![Configurações de logon único do SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "Configurações de logon único do SAML")

7.  Na seção **Adicionar um Novo SAML**, realize as seguintes etapas:

    ![Adicionar um novo SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Adicionar um novo SAML")

    1.  Na caixa de texto **Nome STS**, digite um nome para a sua configuração.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no InsideView**, copie o valor do **Ponto de extremidade iniciado pelo SP (Provedor de Serviços)** e cole-o na caixa de texto **Ponto de extremidade SamlP/WS-Fed Unsolicated**.
    3.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.
        
		>[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado STS**
    5.  Na caixa de texto **Mapeamento de ID de Usuário do Crm**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
6.  Na caixa de texto **Mapeamento de Email do Crm**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
7.  Na caixa de texto **Mapeamento de Nome do Crm**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
8.  Na caixa de texto **Mapeamento de Sobrenome do Crm**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
9.  Clique em **Salvar**.

8.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurar o Logon Único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no InsideView, eles devem ser provisionados no InsideView. No caso do InsideView, o provisionamento é uma tarefa manual.
  
Para obter os usuários ou os contatos criados no InsideView, entre em contato com seu gerente de histórias de sucesso do cliente ou envie um email para ****support@insideview.com**

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do InsideView ou APIs fornecidas pelo InsideView para provisionar as contas de usuário do AD do Azure.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao InsideView, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **InsideView**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-insideview-tutorial/IC794138.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-insideview-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->