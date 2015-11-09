<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Samanage | Microsoft Azure" 
    description="Saiba como usar o Samanage com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#Tutorial: Integração do Active Directory do Azure ao Samanage
  
O objetivo deste tutorial é mostrar a integração do Azure ao Samanage. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Samanage
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Samanage poderão fazer logon único no aplicativo em seu site de empresa do Samanage (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Samanage
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-samanage-tutorial/IC771705.png "Cenário")
##Habilitando a integração de aplicativos para o Samanage
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Samanage.

###Para habilitar a integração de aplicativos com o Samanage, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-samanage-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-samanage-tutorial/IC749321.png "Adicionar aplicativo")

5.  No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-samanage-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Samanage**.

    ![Galeria de aplicativos](./media/active-directory-saas-samanage-tutorial/IC771707.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Samanage** e clique em **Concluir** para adicionar o aplicativo.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Samanage com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Samanage**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Samanage**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-samanage-tutorial/IC771710.png "Logon Único do AD do Microsoft Azure")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de entrada do Samanage**, digite a URL usando o padrão "*https://\<nome-locatário>.samanage.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-samanage-tutorial/IC771711.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Samanage**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Samanage como administrador.

6.  Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.

    ![Painel](./media/active-directory-saas-samanage-tutorial/IC771712.png "Painel")

7.  Clique em **Logon Único**.

    ![Logon Único](./media/active-directory-saas-samanage-tutorial/IC771713.png "Logon Único")

8.  Na página do diálogo **Fazer logon usando o SAML**, realize as seguintes etapas e clique em **Salvar Alterações**:

    1.  Clique em **Habilitar o Logon Único com SAML**. ![Fazer logon usando SAML](./media/active-directory-saas-samanage-tutorial/IC771719.png "Fazer logon usando SAML")
    2.  No portal do Azure, na página do diálogo **Configurar logon único no Samanage**, copie o valor da **ID de Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configurar o logon único")
3.  No portal do Azure, na página do diálogo **Configurar logon único no Samanage**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
    4.  No portal do Azure, na página do diálogo **Configurar logon único no Samanage**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    5.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para sua área de transferência e cole-o na caixa de texto **Certificado X.509**
    7.  Clique em **Criar usuários se eles não existirem no Samanage**.![Atualizar](./media/active-directory-saas-samanage-tutorial/IC771722.png "Atualizar")
    8.  Clique em **Atualizar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Samanage, eles deverão ser provisionados no Samanage. No caso do Samanage, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Samanage**.

2.  Vá para **Painel > Configuração**.

    ![Configuração](./media/active-directory-saas-samanage-tutorial/IC771724.png "Configuração")

3.  Clique na guia **Usuários**

    ![Usuários](./media/active-directory-saas-samanage-tutorial/IC771725.png "Usuários")

4.  Clique em **Novo Usuário**.

    ![Novo Usuário](./media/active-directory-saas-samanage-tutorial/IC771726.png "Novo Usuário")

5.  Digite o **Endereço de Email** e o **Nome** de uma conta do Azure AD que você deseja provisionar e clique em **Criar usuário**.

    >[AZURE.NOTE]O titular da conta do AAD receberá um email e um link para confirmar sua conta antes de se tornar ativo.

    ![Criar Usuário](./media/active-directory-saas-samanage-tutorial/IC771727.png "Criar Usuário")

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Samanage ou as APIs fornecidas pelo Samanage para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Samanage, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Samanage**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-samanage-tutorial/IC771728.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-samanage-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->