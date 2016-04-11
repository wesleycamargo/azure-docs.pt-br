<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao ServiceNow | Microsoft Azure" 
    description="Aprenda a usar o ServiceNow com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais." 
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
    ms.date="01/26/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o ServiceNow
  
O objetivo deste tutorial é mostrar a integração do Azure ao ServiceNow.
 O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário em ServiceNow, versão Calgary ou superior
-   O locatário ServiceNow deve ter o [Plug-in de Logon Único de Provedor Múltiplo](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito enviando uma solicitação de serviço em https://hi.service-now.com/ 
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ServiceNow poderão fazer logon único no aplicativo em seu site de empresa do ServiceNow (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o ServiceNow
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Cenário")
##Habilitando a integração de aplicativos para o ServiceNow
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ServiceNow.

###Para habilitar a integração de aplicativos com o ServiceNow, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ServiceNow**.

    ![Galeria de aplicativos](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ServiceNow** e clique em **Concluir** para adicionar o aplicativo.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ServiceNow com sua conta do AD do Azure usando federação baseada em protocolo SAML.

Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Dropbox for Business. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure AD, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no ServiceNow**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar logon único")

3.  Na página **Definir Configurações do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar a URL do aplicativo")

    a. Na caixa de texto **URL de logon ServiceNow**, digite a URL usada pelos usuários para entrar no seu aplicativo ServiceNow (p. ex.: *https://\< InstanceName > service-now.com*).

    b. Na caixa de texto **URL do Emissor**, digite a URL usada pelos usuários para entrar no seu aplicativo ServiceNow (p. ex.: *https://\< InstanceName > service-now.com*).

    c. Clique em **Próximo**.

4.  Para que o Azure AD configure automaticamente o ServiceNow para autenticação com base em SAML, insira o nome da instância ServiceNow, o nome de usuário administrador e a senha de administrador no formulário **Configurar automaticamente o logon único** e clique em *Configurar*. Observe que o nome de usuário de administrador informado deve ter a função **security\_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como provedor de identidade SAML, clique em **Configurar manualmente o aplicativo para logon único** e, em seguida, clique em **Avançar** e conclua as etapas seguintes.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar a URL do aplicativo")



5.  Na página **Configurar logon único no ServiceNow**, clique em **Baixar certificado**, salve o arquivo de certificado no computador e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar logon único")

1. Entre no seu aplicativo ServiceNow como administrador.

2. No painel de navegação à esquerda, clique em **Propriedades**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configurar a URL do aplicativo")


3. No diálogo **Várias propriedades de SSO do provedor**, execute as seguintes etapas:

    ![Configurar a URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar a URL do aplicativo")

    a. Como **Habilitar vários provedores SSO**, selecione **Sim**.

    b. Como **Habilitar log de depuração com integração SSO de vários provedores**, selecione **Sim**.

    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome\_de\_usuário**.

    d. Clique em **Salvar**.



1. No painel de navegação à esquerda, clique em **Certificados x509**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configurar logon único")


1. No diálogo **Certificados x. 509**, clique em **Novo**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar logon único")


1. No diálogo **Certificados x. 509**, execute as seguintes etapas:

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar logon único")

    a. Clique em **Novo**.

    b. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    c. Selecione **Ativo**.

    d. Para **Formato**, selecione **PEM**.

    e. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    f. Crie um arquivo codificado em base-64 usando o certificado baixado.
    > [AZURE.NOTE] Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    
    g. Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado PEM**.

    h. Clique em **Atualizar**.


1. No painel de navegação à esquerda, clique em **Provedores de Identidade**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configurar logon único")

1. No diálogo **Provedores de Identidade**, clique em **Novo**:

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar logon único")


1. No diálogo **Provedores de Identidade**, clique em **SAML2 Atualização1?**:

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar logon único")


1. No diálogo Propriedades de SAML2 Atualização1, execute as seguintes etapas:

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar logon único")


    Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. Na caixa de texto **Campo de Usuário**, digite **email** ou **user\_id**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.
    
    **Observação:** você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal clássico do Azure e o mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user\_id)

    c. No portal clássico do Azure AD, copie o valor **ID do Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.

    d. No portal clássico do Azure AD, copie o valor **URL de Solicitação de Autenticação** e cole-o na caixa de texto **Solicitação de Autenticação do Provedor de Identidade**.

    e. No portal clássico do Azure AD, copie o valor **URL do Serviço de Logon Único** e cole-o na caixa de texto **Solicitação de Logon Único do Provedor de Identidade**.

    f. Na caixa de texto **Home page do ServiceNow**, digite a URL da sua página inicial de instância do ServiceNow.

    > [AZURE.NOTE] A home page da instância do ServiceNow é uma concatenação da **URL do locatário ServieNow** e **/navpage.do** (p. ex.: **https://fabrikam.service-now.com/navpage.do*).
 

    g. Na caixa de texto **ID da entidade/emissor**, digite a URL do seu locatário do ServiceNow.

    h. Na caixa de texto **URL do Público-alvo**, digite a URL do seu locatário do ServiceNow.

    i. Na caixa de texto **associação de protocolo SingleLogoffRequest do DP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Na caixa de texto Política da NameID, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Desmarque **Criar um AuthnContextClass**.

    l. No **método AuthnContextClassRef**, digite ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. Na caixa de texto **Distorção do Relógio**, digite **60**.

    n. Como **Script de Logon Único**, selecione **MultiSSO\_SAML2\_Update1**.

    o. Como **Certificado x509**, selecione o certificado que você criou na etapa anterior.

    p. Clique em **Enviar**.



6. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar logon único")

7. Na página **Confirmação de logon único**, clique em **Concluir**.
 
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar logon único")



##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no ServiceNow.


### Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. No portal clássico de Gerenciamento do Azure, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar provisionamento do usuário**. <br><br> ![Provisionamento do usuário](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisionamento do usuário")


2. Na página **Inserir suas credenciais do ServiceNow para habilitar o provisionamento automático de usuário**, forneça as seguintes configurações: Configurar Provisionamento de Usuário

     2.1. Na caixa de texto **Nome de Instância do ServiceNow**, digite o nome da instância do ServiceNow.

     2.2. Na caixa de texto **Nome de Usuário Administrador do ServiceNow**, digite o nome da conta de administrador do ServiceNow.

     2.3. Na caixa de texto **Senha de Administrador do ServiceNow**, digite a senha desta conta.

     2.4. Clique em **validar** para verificar sua configuração.

     2.5. Clique no botão **Avançar** para abrir a página **Próximas etapas**.

     2.6. Se você deseja provisionar todos os usuários neste aplicativo, selecione “**Provisionar automaticamente todas as contas de usuário no diretório neste aplicativo**”. <br><br> ![Próximas etapas](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Próximas etapas")

     2.7. Na página **Próximas etapas**, clique em **Concluir** para salvar sua configuração.











##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao ServiceNow, execute as seguintes etapas:

1.  No portal clássico do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ServiceNow**, clique **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!-----------HONumber=AcomDC_0330_2016-->