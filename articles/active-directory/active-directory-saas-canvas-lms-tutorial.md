<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Canvas | Microsoft Azure" 
    description="Saiba como usar o Canvas LMS com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Canvas LMS

O objetivo deste tutorial é mostrar a integração do Azure ao Canvas. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Canvas

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Canvas poderão fazer logon único no aplicativo em seu site de empresa do Canvas (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Canvas
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Cenário")
##Habilitando a integração de aplicativos para o Canvas

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Canvas.

###Para habilitar a integração de aplicativos para o Canvas, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Canvas**.

    ![Galeria de Aplicativos](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Canvas** e clique em **Concluir** para adicionar o aplicativo.

    ![Tela](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Tela")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Canvas com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Configurar o logon único para o Canvas exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Canvas**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Canvas**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Canvas**, digite a URL usando o padrão `https://<tenant-name>.instructure.com` e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Canvas**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Canvas como um administrador.

6.  Vá para **Cursos > Contas Gerenciadas > Microsoft**.

    ![Tela](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

7.  No painel de navegação à esquerda, selecione **Autenticação** e clique em **Adicionar Nova Config. do SAML**.

    ![Autenticação](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Autenticação")

8.  Na página de integração atual, execute as seguintes etapas:

    ![Integração Atual](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Integração Atual")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no Canvas**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IdP**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Canvas**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    3.  No portal do Azure, na página do diálogo **Configurar logon único no Canvas**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logoff**.
    4.  No portal do Azure, na página do diálogo **Configurar logon único no Canvas**, copie o valor da **URL de Alteração de Senha** e cole-o na caixa de texto **URL de Alteração de Senha**.
    5.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado**.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Na lista **Atributo de Logon**, selecione **NameID**.
    7.  Na lista **Formato de Identificador**, selecione **emailAddress**.
    8.  Clique em **Salvar Configurações de Autenticação**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Canvas, eles devem ser provisionados no Canvas. No caso do Canvas, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Canvas**.

2.  Vá para **Cursos > Contas Gerenciadas > Microsoft**.

    ![Tela](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

3.  Clique em **Usuários**.

    ![Usuários](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Usuários")

4.  Clique em **Adicionar Novo Usuário**.

    ![Usuários](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Usuários")

5.  Na página da caixa de diálogo Adicionar um Novo Usuário, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Adicionar usuário")

    1.  Na caixa de texto **Nome Completo**, digite o nome do usuário.
    2.  Na caixa de texto **Email**, digite o endereço de email do usuário.
    3.  Na caixa de texto **Logon**, digite o endereço de email do AD do Azure do usuário.
    4.  Selecione **Enviar email ao usuário sobre a criação desta conta**.
    5.  Clique em **Adicionar Usuário**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Canvas ou as APIs fornecidas pelo Canvas para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Canvas, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Canvas**, clique em **Atribuir usuários**.

    ![Atribuindo usuários](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Atribuindo usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->