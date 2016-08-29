<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com a Área Restrita Salesforce | Microsoft Azure"
    description="Saiba como usar a Área Restrita Salesforce com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" 
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
    ms.date="08/12/2016" 
    ms.author="jeedes" />


#Tutorial: Integração do Active Directory do Azure com a Área Restrita Salesforce
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=521878).
  
O objetivo deste tutorial é mostrar a integração do Azure com a Área Restrita Salesforce. As áreas restritas oferecem a capacidade de criar várias cópias da sua organização em ambientes separados por uma variedade de finalidades, como desenvolvimento, testes e treinamento, sem comprometer os dados e os aplicativos em sua organização de produção do Salesforce. Para obter mais detalhes, confira [Visão geral da área restrita](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma área restrita no Salesforce.com
  
Se você não tiver uma área restrita válida no Salesforce.com, precisará entrar em contato com o Salesforce.
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para a Área Restrita Salesforce
2.  Configurando o logon único
3.  Habilitando seu domínio
4.  Configurando o provisionamento de usuários
5.  Atribuindo usuários

![Cenário](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Cenário")
##Habilitando a integração de aplicativos para a Área Restrita Salesforce
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para a área restrita Salesforce.

###Para habilitar a integração de aplicativos para a área restrita Salesforce, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de Aplicativos**, clique em **Adicionar um Aplicativo** e em **Adicionar um aplicativo a ser utilizado pela minha organização**.

    ![O que você deseja fazer?](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "O que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Área Restrita Salesforce**.

    ![Galeria de Aplicativos](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galeria de Aplicativos")

6.  No painel de resultados, selecione **Área Restrita Salesforce** e clique em **Concluir** para adicionar o aplicativo.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Salesforce com a conta do AD do Azure usando federação baseada em protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No Portal Clássico do Azure, na página de integração de aplicativos da **Área Restrita do Salesforce**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon na Área Restrita Salesforce**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon**, digite sua URL usando o seguinte padrão, "*`http://company.my.salesforce.com` e, em seguida, clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar a URL do Aplicativo")

4. Se já tiver configurado o logon único para outra instância de Área restrita do Salesforce em seu diretório, você também deve configurar o **Identificador** para ter o mesmo valor que a **URL de Logon**. O campo **Identificador** pode ser encontrado marcando a caixa de seleção **Mostrar configurações avançadas** na página **Configurar URL do Aplicativo** do diálogo.

4.  Na página **Configurar logon único na Área Restrita Salesforce**, clique em **Baixar o certificado** e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em sua área restrita Salesforce como um administrador.

6.  No menu na parte superior, clique em **Configuração**.

    ![Configuração](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Configuração")

7.  No painel de navegação à esquerda, clique em **Controles de Segurança** e clique em **Configurações de Logon Único**.

    ![Configurações de Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Configurações de Logon Único")

8.  Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Configurações de Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Configurações de Logon Único")

    a. Selecione **SAML Habilitado**.
    
    b. Clique em **Novo**.

9.  Na seção Configurações de Logon Único de SAML, execute as seguintes etapas:

    ![Configurações de Logon Único do SAML](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Configurações de Logon Único do SAML")

    a. Na caixa de texto Nome, digite o nome da configuração (por exemplo:*SPSSOWAAD\_Teste*).
    
    b. No portal clássico do Azure, na página de diálogo **Configurar logon único na Área Restrita do Salesforce**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor**.
    
    c. Na caixa de texto **Id da Entidade**, digite **https://test.salesforce.com** se esta for a primeira instância de área restrita do Salesforce que você está adicionando ao seu diretório. Se você já tiver adicionado uma instância da Área restrita do Salesforce, para a **ID da Entidade**, digite a **URL de Logon que deve estar no seguinte formato: `http://company.my.salesforce.com`
    
    d. Clique em **Procurar** para carregar o certificado baixado.
    
    e. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**.
    
    f. Para **Local de Identidade SAML**, selecione **A identidade está no elemento NameIdentifier da instrução Subject**.
    
    g. No portal clássico do Azure, na página de diálogo **Configurar logon único na Área Restrita do Salesforce**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
    
    h. O SFDC não dá suporte a logout SAML. Como alternativa, cole 'https://login.windows.net/common/wsfederation?wa=wsignout1.0' na caixa de texto **URL de Logout do Provedor de Identidade**.
    
    i. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP POST**.
    
    j. Clique em **Salvar**.

10. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar o logon único")

##Habilitando seu domínio
  
Esta seção pressupõe que você já tenha criado um domínio. Para obter mais detalhes, confira [Definindo seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###Para habilitar seu domínio, execute as seguintes etapas:

1.  No painel de navegação esquerdo, clique em **Gerenciamento de Domínio** e clique em **Meu Domínio.**

    ![Meu Domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Meu Domínio")

    >[AZURE.NOTE]Verifique se o domínio foi configurado corretamente.

2.  Na seção **Configurações de Página de Logon**, clique em **Editar** e, para o **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único SAML da seção anterior e, por fim, clique em **Salvar**.

    ![Meu Domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Meu Domínio")
  
Assim que você tiver um domínio configurado, seus usuários deverão usar a URL do domínio para fazer logon na área restrita Salesforce. Para obter o valor da URL, clique no perfil SSO criado na seção anterior.
##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory na Área Restrita Salesforce.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  No portal do Salesforce, na barra de navegação superior, selecione seu nome para expandir o seu menu de usuário:

    ![Minhas Configurações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Minhas Configurações")

2.  Do seu menu de usuário, selecione **Minhas Configurações** para abrir a página **Minhas Configurações**.

3.  No painel esquerdo, clique em **Pessoal** para expandir a seção Pessoal e clique em **Redefinir Meu Token de Segurança**:

    ![Minhas Configurações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Minhas Configurações")

4.  Na página **Redefinir meu Token de Segurança**, clique em **Redefinir Token de Segurança** para solicitar um email com seu token de segurança do Salesforce.com.

    ![Novo Token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Novo Token")

5.  Marque a caixa de entrada de um email do Salesforce.com usando "**confirmação de segurança de salesforce.com.com**" como o assunto.

6.  Leia o email e copie o valor do token de segurança.

7.  No portal clássico do Azure, na página de integração do aplicativo **Área restrita do salesforce**, clique em **Configurar provisionamento do usuário** para abrir a caixa de diálogo **Configurar Provisionamento do Usuário**.

    ![Configure o provisionamento do usuário](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure o provisionamento do usuário")

8.  Na página **Inserir suas credenciais da Área Restrita Salesforce para habilitar o provisionamento automático de usuários**, forneça as seguintes configurações:

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    a. Na caixa de texto **Nome de Usuário de Administrador da área restrita Salesforce**, digite o nome da conta de uma área restrita Salesforce com o perfil **Administrador de Sistema** do Salesforce.com atribuído.

    b. Na caixa de texto **Senha do Administrador da Área Restrita Salesforce**, digite a senha dessa conta.

    c. Na caixa de texto **Token de Segurança do Usuário**, cole o valor do token de segurança.

    d. Clique em **Validar** para verificar sua configuração.

    e. Clique no botão **Próximo** para abrir a página **Confirmação**.

9.  Na página **Confirmação**, clique em **Concluir** para salvar sua configuração.
##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários à Área Restrita Salesforce, execute as etapas a seguir:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos da **Área Restrita Salesforce**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sim")
  
Agora você deve aguardar 10 minutos e verificar se a conta foi sincronizada com a Área Restrita Salesforce.
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_0817_2016-->