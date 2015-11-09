<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Freshdesk | Microsoft Azure" 
    description="Saiba como usar o Freshdesk com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: integração do Active Directory do Azure ao Freshdesk
  
O objetivo deste tutorial é mostrar a integração do Azure ao Freshdesk. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Freshdesk
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Freshdesk poderão fazer logon único no aplicativo em seu site de empresa do Freshdesk (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para Freshdesk
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Cenário")
##Habilitando a integração de aplicativos para Freshdesk
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Freshdesk.

###Para habilitar a integração de aplicativos para o Freshdesk, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Freshdesk**.

    ![Galeria de aplicativos](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Freshdesk** e clique em **Concluir** para adicionar o aplicativo.

    ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Freshdesk com a própria conta no Azure AD usando federação baseada no protocolo SAML. A configuração do logon único para Freshdesk exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Freshdesk**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Freshdesk**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Freshdesk**, digite a URL usando o padrão "*https://\<nome-locatário>.Freshdesk.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Freshdesk**, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador como **c:\\Freshdesk.cer**.

    ![Configurar o logon único](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

6.  No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrador")

7.  Na guia **Configurações Gerais**, clique em **Segurança**.

    ![Segurança](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Segurança")

8.  Na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Logon Único")

    1.  Para **SSO (Logon Único)**, selecione **Ativado**.
    2.  Selecione **SSO do SAML**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Freshdesk**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do SAML**.
    4.  No portal do Azure, na página do diálogo **Configurar logon único no Freshdesk**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do SAML**.
    5.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado de Segurança**.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no Freshdesk, eles devem ser provisionados no Freshdesk. No caso do Freshdesk, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Freshdesk**.

2.  No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrador")

3.  Na guia **Configurações Gerais**, clique em **Agentes**.

    ![Agentes](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agentes")

4.  Clique em **Novo Agente**.

    ![Novo Agente](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Novo Agente")

5.  Na caixa de diálogo Informações sobre o Agente, execute as seguintes etapas:

    ![Informações sobre o Agente](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informações sobre o Agente")

    1.  Na caixa de texto **Nome Completo**, digite o nome da conta do Azure AD que você deseja provisionar.
    2.  Na caixa de texto **Email**, digite o endereço de email do Azure AD da conta do Azure AD que você deseja provisionar.
    3.  Na caixa de texto **Título**, digite a conta do Azure AD que você deseja provisionar.
    4.  Selecione **Função de agentes** e clique em **Atribuir**.
    5.  Clique em **Salvar**.
    
        >[AZURE.NOTE]O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Freshdesk, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Freshdesk**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->