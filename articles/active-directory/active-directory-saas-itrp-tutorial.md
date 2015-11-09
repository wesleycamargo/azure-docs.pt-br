<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure com o ITRP | Microsoft Azure" 
    description="Saiba como usar o ITRP com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="10/20/2015" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure com o ITRP
  
O objetivo deste tutorial é mostrar a integração do Azure com o ITRP. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do ITRP
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ITRP poderão fazer logon único no aplicativo em seu site de empresa do ITRP (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o ITRP
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-itrp-tutorial/IC775551.png "Cenário")
##Habilitando a integração de aplicativos para o ITRP
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ITRP.

###Para habilitar a integração de aplicativos com o ITRP, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-itrp-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-itrp-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-itrp-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ITRP**.

    ![Galeria de Aplicativos](./media/active-directory-saas-itrp-tutorial/IC775565.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ITRP** e clique em **Concluir** para adicionar o aplicativo.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ITRP com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para ITRP exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **ITRP**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no ITRP**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do ITRP**, digite a URL usando o padrão "*https://\<nome-locatário>.ITRP.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no ITRP**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\ITRP.cer**.

    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do ITRP como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Configurações**.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  No painel de navegação à esquerda, selecione **Logon Único**.

    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775571.png "Logon Único")

8.  Na seção de configuração de logon único, execute as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775572.png "Logon Único")

    ![Logon Único](./media/active-directory-saas-itrp-tutorial/IC775573.png "Logon Único")

    1.  Clique em **Habilitar**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no ITRP**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff Remoto**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no ITRP**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de SSO do SAML**.
    4.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado**.
        
		>[AZURE.TIP]Para obter mais detalhes, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no ITRP, eles devem ser provisionados no ITRP. No caso do ITRP, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **ITRP**.

2.  Na barra de ferramentas na parte superior, clique em **Registros**.

    ![Administrador](./media/active-directory-saas-itrp-tutorial/IC775575.png "Administrador")

3.  No menu pop-up, selecione **Pessoas**.

    ![Pessoas](./media/active-directory-saas-itrp-tutorial/IC775587.png "Pessoas")

4.  Clique em **Adicionar Nova Pessoa** (“+”).

    ![Administrador](./media/active-directory-saas-itrp-tutorial/IC775576.png "Administrador")

5.  Na caixa de diálogo Adicionar nova pessoa, execute as seguintes etapas:

    ![Usuário](./media/active-directory-saas-itrp-tutorial/IC775577.png "Usuário")

    1.  Digite o **Nome** e **Email** de uma conta válida do AAD que você deseja provisionar.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do ITRP ou APIs fornecidas pelo ITRP para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao ITRP, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ITRP**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-itrp-tutorial/IC775588.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-itrp-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->