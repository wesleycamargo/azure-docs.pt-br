<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Thoughtworks Mingle | Microsoft Azure" 
    description="Saiba como usar o Thoughtworks Mingle com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
     manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Thoughtworks Mingle
  
O objetivo deste tutorial é mostrar a integração do Azure com o Thoughtworks Mingle. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Thoughtworks Mingle
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Thoughtworks Mingle
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Cenário")

##Habilitando a integração de aplicativos com o Thoughtworks Mingle
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Thoughtworks Mingle.

###Para habilitar a integração de aplicativos com o Thoughtworks Mingle, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Thoughtworks Mingle**.

    ![Galeria de Aplicativos](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Thoughtworks Mingle** e clique em **Concluir** para adicionar o aplicativo.

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Thoughtworks Mingle com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário carregar um certificado para Thoughtworks Mingle.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Thoughtworks Mingle**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Thoughtworks Mingle**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Locatário do Thoughtworks Mingle**, digite a URL usando o padrão "**http://company.mingle.thoughtworks.com**" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar o logon único no Thoughtworks Mingle**, clique em Baixar metadados e salve-o no computador.

    ![Configurar logon único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurar o logon único")

5.  Faça logon em seu site de empresa do **Thoughtworks Mingle** como administrador.

6.  Clique na guia **Administrador** e em **Config. de SSO**.

    ![Config. de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Config. de SSO")

7.  Na seção **Config. de SSO**, realize as seguintes etapas:

    ![Config. de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Config. de SSO")

    1.  Para carregar o arquivo de metadados, clique em **Escolher arquivo**.
    2.  Clique em **Salvar Alterações**.

8.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para usuários do AAD conseguirem efetuar logon, eles devem ser provisionados para o aplicativo Thoughtworks Mingle usando seus nomes de usuário do Active Directory do Azure. No caso do Thoughtworks Mingle, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa Thoughtworks Mingle como um administrador.

2.  Clique em **Perfil**.

    ![Seu primeiro projeto](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Seu primeiro projeto")

3.  Clique na guia **Administrador** e em **Usuários**.

    ![Usuários](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Usuários")

4.  Clique em **Novo Usuário**.

    ![Novo Usuário](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Novo Usuário")

5.  Na página do diálogo **Novo Usuário**, realize as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Novo Usuário")

    1.  Digite o **Nome de entrada**, **Nome de exibição**, **Escolher senha** e **Confirmar senha** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Para **Tipo de usuário**, selecione **Usuário completo**.
    3.  Clique em **Criar este Perfil**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do Thoughtworks Mingle ou as APIs fornecidas pelo Thoughtworks Mingle para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Thoughtworks Mingle, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Thoughtworks Mingle**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0413_2016-->