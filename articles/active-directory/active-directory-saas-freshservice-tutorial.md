<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao FreshService | Microsoft Azure" 
    description="Saiba como usar o FreshService com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao FreshService
  
O objetivo deste tutorial é mostrar a integração do Azure ao FreshService. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do FreshService com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao FreshService poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o FreshService
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Cenário")
##Habilitando a integração de aplicativos com o FreshService
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o FreshService.

###Para habilitar a integração de aplicativos para o FreshService, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **FreshService**.

    ![Galeria de Aplicativos](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **FreshService** e clique em **Concluir** para adicionar o aplicativo.

    ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no FreshService com a própria conta do Azure AD usando a federação baseada no protocolo SAML. A configuração do logon único para FreshService exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **FreshService**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no FreshService**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurar o logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do FreshService**, digite a URL usada pelos usuários para entrar no aplicativo Freshdesk (por exemplo: "*http://democompany.freshservice.com/*") e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no FreshService**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do FreshService como administrador.

6.  No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

7.  No **Portal do Cliente**, clique em **Segurança**.

    ![Segurança](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Segurança")

8.  Na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Logon Único")

    1.  Ative o **Logon Único**.
    2.  Selecione **SSO do SAML**.
    3.  No portal clássico do Azure, na página de diálogo **Configurar logon único no FreshService**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do SAML**.
    4.  No portal clássico do Azure, na página de diálogo **Configurar logon único no FreshService**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    5.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado de Segurança**.
    
        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no FreshService, eles devem ser provisionados no FreshService. No caso do FreshService, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **FreshService** como administrador.

2.  No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administrador")

3.  Na seção **Gerenciamento de Usuários**, clique em **Solicitantes**.

    ![Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Solicitantes")

4.  Clique em **Novo Solicitante**.

    ![Novos Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790819.png "Novos Solicitantes")

5.  Na seção **Novo Solicitante**, realize as seguintes etapas:

    ![Novo Solicitante](./media/active-directory-saas-freshservice-tutorial/IC790820.png "Novo Solicitante")

    1.  Insira os atributos **Nome** e **Email** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do FreshService ou APIs fornecidas pelo FreshService para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao FreshService, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **FreshService**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->