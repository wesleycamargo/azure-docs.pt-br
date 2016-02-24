<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao ClickTime | Microsoft Azure" 
    description="Saiba como usar o ClickTime com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao ClickTime

O objetivo deste tutorial é mostrar a integração do Azure ao ClickTime. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do ClickTime

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ClickTime poderão fazer logon único no aplicativo em seu site de empresa do ClickTime (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o ClickTime
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Cenário")
##Habilitando a integração de aplicativos para o ClickTime

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o ClickTime.

###Para habilitar a integração de aplicativos para o ClickTime, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **ClickTime**.

    ![Galeria de aplicativos](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **ClickTime** e clique em **Concluir** para adicionar o aplicativo.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no ClickTime com a própria conta no Azure AD usando federação baseada no protocolo SAML. Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do ClickTime. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Para poder configurar o logon único em seu locatário do ClickTime, você precisa primeiro contatar o suporte técnico do ClickTime para que esse recurso seja habilitado.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **ClickTime**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Habilitar logon único](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Habilitar logon único")

2.  Na página **Como você deseja que os usuários façam logon no ClickTime**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configurar logon único")

3.  Na página **Configurar logon único no ClickTime**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configurar logon único")

4.  Em outra janela do navegador da Web, faça logon em seu site de empresa do ClickTime como administrador.

5.  Na barra de ferramentas na parte superior, clique em **Preferências** e em **Configurações de Segurança**.

6.  Na seção de configuração de **Preferências de Logon Único**, realize as seguintes etapas:

    ![Configurações de Segurança](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Configurações de Segurança")

    1.  Selecione **Permitir** a entrada usando o SSO (Logon Único) com **OneLogin**.
    2.  No portal do Azure, na página do diálogo **Configurar logon único no ClickTime**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **Ponto de Extremidade do Provedor de Identidade**.
    3.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra seu certificado codificado em Base 64 no **Bloco de Notas**, copie o conteúdo e cole-o na caixa de texto **Certificado X.509**.
    5.  Clique em **Salvar**.

7.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no ClickTime, eles devem ser provisionados no ClickTime. No caso do ClickTime, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário do **ClickTime**.

2.  Na barra de ferramentas na parte superior, clique na **Empresa** e em **Pessoas**.

    ![Pessoas](./media/active-directory-saas-clicktime-tutorial/IC777282.png "Pessoas")

3.  Clique em **Adicionar Pessoa**.

    ![Adicionar Pessoa](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Adicionar Pessoa")

4.  Na seção Nova Pessoa, execute as etapas a seguir:

    ![Pessoas](./media/active-directory-saas-clicktime-tutorial/IC777284.png "Pessoas")

    1.  Na caixa de texto **endereço de email**, digite o endereço de email de sua conta do Azure AD.
    2.  Na caixa de texto **nome completo**, digite o nome de sua conta do Azure AD.  

        >[AZURE.NOTE]Se quiser, defina propriedades adicionais do novo objeto pessoa.

    3.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do ClickTime ou as APIs fornecidas pelo ClickTime para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao ClickTime, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **ClickTime**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->