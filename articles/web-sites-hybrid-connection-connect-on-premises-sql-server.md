<properties title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas

##Introdução##
As Conexões Híbridas podem conectar sites do Microsoft Azure websites a recursos locais que utilizem uma porta TCP estática. Os recursos com suporte incluem Microsoft SQL Server, MySQL, APIs Web HTTP, Serviços Móveis e os serviços Web mais personalizados. 

Neste tutorial, você aprenderá como criar um site no Portal de Visualização do Azure, conectar o site a seu banco de dados local de SQL Server utilizando o novo recurso de Conexão Híbrida, crie um aplicativo Web ASP.NET simples que utilizará a conexão híbrida e implantará o aplicativo no Site do Azure. O site completo no Azure armazena credenciais de usuário em um banco de dados de associação local. O tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure nem com o ASP.NET.

> [WACOM.NOTE] A porção Sites do recurso Conexões Híbridas está disponível somente no [portal Visualização do Azure](https://portal.azure.com). Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Pré-requisitos##
Para concluir este tutorial, você precisará dos produtos a seguir. Todos estão disponíveis em versões gratuitas, portanto, é possível começar a desenvolver para o Azure de maneira totalmente gratuita.

- **Assinatura do Azure** - para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pt-br/pricing/free-trial/). 

- **Visual Studio 2013** - Para baixar uma versão de avaliação gratuita do Visual Studio 2013, consulte [Downloads do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale isso antes de continuar.

- **Microsoft .NET Framework 3.5 Service Pack 1** - se o seu sistema operacional for Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, você pode habilitar esse item em Painel de Controle > Programas e Recursos > Ativar ou desativar recursos do Windows. Caso contrário, você pode baixá-lo do [Centro de Download da Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express with Tools** - baixe o Microsoft SQL Server Express gratuitamente na [página do Banco de dados da plataforma da web da Microsoft](http://www.microsoft.com/web/platform/database.aspx). Escolha a versão **Express** (não LocalDB). A versão **Express with Tools** inclui o SQL Server Management Studio, que você usará neste tutorial.

- **SQL Server Management Studio Express** - este item está incluso no item para download SQL Server 2014 Express with Tools mencionado acima, mas se você precisar instalá-lo separadamente, poderá baixá-lo e instalá-lo na [página de download do SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

O tutorial pressupõe que você tem uma assinatura do Azure, que você tem o Visual Studio 2013 instalado e que você instalou ou habilitou o .NET Framework 3.5. O tutorial mostra a você como instalar o SQL Server 2014 Express em uma configuração que funciona bem com o recurso de Conexões Híbridas do Azure (uma instância padrão com uma porta TCP estática). Antes de iniciar o tutorial, baixe o SQL Server 2014 Express with Tools do local mencionado acima, se você ainda não tem o SQL Server instalado.

### Observações ###
Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. As instâncias padrão no SQL Server usam a porta estática 1433, ao passo que instâncias nomeadas não. 

O computador no qual você instala o agente do Hybrid Connection Manager local:

- Deve ter conectividade de saída com o Azure por:

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Porque</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Obrigatório</strong> para porta HTTP para validação de certificado e, opcionalmente, para conectividade de dados.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Opcional</strong> para conectividade de dados. Se a conectividade de saída para 443 estiver indisponível, a porta TCP 80 é usada.</td>
    </tr>
	<tr>
        <td>5671 e 9352</td>
        <td><strong>Recomendadas</strong> , mas Opcional para conectividade de dados. Observe que esse modo costuma produzir uma taxa de transferência maior. Se a conectividade de saída para essas portas estiver indisponível, a porta TCP 443 é usada.</td>
	</tr>
</table>

- Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local. 

As etapas neste artigo pressupõem que você está utilizando o navegador no computador que hospedará o agente local de conexão híbrida.

Se você já tiver o SQL Server instalado em uma configuração e em um ambiente que atenda às condições descritas acima, é possível avançar e iniciar com [Criar um banco de dados SQL Server local](#CreateSQLDB).

##Neste artigo##
[A. Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local](#InstallSQL)

[B. Criar um site no Portal de Visualização do Azure](#CreateSite)

[C. Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC)

[D. Instalar o Gerenciador de Conexão Híbrida local para completar a conexão](#InstallHCM)

[E. Criar um projeto Web ASP.NET básico, editar a cadeia de conexão de banco de dados e executar o projeto localmente](#CreateASPNET)

[F. Publicar o aplicativo Web no Azure e testá-lo](#PubNTest)

<a name="InstallSQL"></a>
## A. Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local ##

Esta seção mostra a você como instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados, de modo que seu aplicativo Web funcionará com o ambiente de Visualização do Azure.

###Instalar SQL Server Express###

1. Para instalar o SQL Server Express, execute o arquivo **SQLEXPRWT_x64_ENU.exe** ou **SQLEXPR_x86_ENU.exe** que você baixou.O assistente do Centro de Instalação do SQL Server é exibido.
	
	![SQL Server Install][SQLServerInstall]
	
2. Selecione **instalação autônoma novo SQL Server ou adicionar recursos a uma instalação existente**. Siga as instruções, aceitando as configurações e escolhas padrão, até chegar à página **Configuração de Instância**.
	
3. Na página **Configuração de Instância**, escolha **Instância padrão**.
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	Por padrão, a instância padrão do SQL Server atenta-se a solicitações de clientes de SQL Server na porta estática 1433, que é aquela requerida pelo recurso de Conexões Híbridas. Instâncias nomeadas utilizam portas dinâmicas e UDP, que não têm suporte em Conexões Híbridas.
	
4. Aceite os padrões na página **Configuração de Servidor**.
	
5. Na página **Configuração do Mecanismo do Banco de Dados**, em **Modo de Autenticação**, selecione **Modo Misto (autenticação de SQL Server e autenticação do Windows)** e forneça uma senha.
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	Neste tutorial, você utilizará autenticação de SQL Server. Certifique-se de lembrar da senha fornecida por você, porque você precisará dela mais tarde.
	
6. Passe pelas etapas do restante do assistente para concluir a instalação.

###Habilitar TCP/IP###
Para habilitar TCP/IP, você utilizará o SQL Server Configuration Manager, que foi instalado quando você instalou o SQL Server Express. Siga as etapas em [Habilitar Protocolo de Rede TCP/IP para SQL Server](http://technet.microsoft.com/pt-br/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
###Crie um Banco de Dados SQL Server local###

Seu aplicativo Web do Visual Studio exige um banco de dados de associação que possa ser acessado pelo Azure. Isso exige um banco de dados SQL Server ou SQL Server Express (e não o banco de dados LocalDB que o modelo MVC utiliza por padrão), de modo que você criará o banco de dados de associação a seguir. 

1. No SQL Server Management Studio, conecte-se ao SQL Server que você acabou de instalar. (Se o diálogo **Conectar ao servidor** não aparecer automaticamente, navegue para o **Gerenciador de objetos** no painel esquerdo, clique em **Conectar**e depois clique em **Mecanismo de banco de dados**.) 	
	![Connect to Server][SSMSConnectToServer]
	
	Para **Tipo de servidor**, selecione **Mecanismo de Banco de Dados**. Como **Nome do Servidor**, você pode utilizar **localhost** ou o nome do computador que você está utilizando. Escolha **Autenticação do SQL Server**, então faça logon com o nome de usuário SA e senha para autenticação de servidor que você criou anteriormente. 
	
2. Para criar um novo banco de dados utilizando o SQL Server Management Studio, clique com o botão direito **Bancos de Dados** no Gerenciador de Objetos, então clique em **Novo Bancos de Dados**.
	
	![Create new database][SSMScreateNewDB]
	
3. Na caixa de diálogo **Novo Banco de Dados**, insira MembershipDB como nome do banco de dados e depois clique em **OK**. 
	
	![Provide database name][SSMSprovideDBname]
	
	Observe que você não precisa fazer nenhuma alteração ao banco de dados nesse ponto. As informações de associação serão adicionadas de modo automático posteriormente pelo seu aplicativo Web, quando você executá-lo.
	
4. No Gerenciador de Objetos, se você expandir o item **Bancos de Dados**, verá que o banco de dados de associação foi criado.
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B. Criar um site no Portal de Visualização do Azure ##

> [WACOM.NOTE] Se você já criou um site no Portal de Visualização do Azure que você deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC) e continuar dali.

1. No canto inferior esquerdo do [Portal de visualização do Azure](https://portal.azure.com), clique em **Novo** e escolha **Site**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. Na lâmina **Site**, forneça um nome para seu site e em seguida clique em **Criar**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Após alguns instantes, o site é criado e sua lâmina de site aparece. A lâmina é um painel com rolagem vertical, que permite a você gerenciar o seu site.
	
	![Website running][WebSiteRunningBlade]
	
4. Para verificar se o site está funcionando, você pode clicar no ícone **Procurar** para exibir a página padrão.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
Em seguida, você criará uma conexão híbrida e um serviço do BizTalk para o site.

<a name="CreateHC"></a>
## C. Criar uma Conexão Híbrida e um Serviço do BizTalk ##

1. De volta ao Portal de Visualização, role para baixo pela lâmina para seu site e selecione **Conexões Híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Na lâmina Conexões Híbridas, clique em **Adicionar**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. A lâmina **Adicionar uma conexão híbrida** se abre.  Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a lâmina **Criar Conexão Híbrida** se abre para você.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Na lâmina **Criar Conexão Híbrida**:
	- Como **Nome**, forneça um nome para a conexão.
	- Como **Hostname**, insira o nome do computador que hospeda o seu SQL Server.
	- Como **Porta**, insira 1433 (a porta padrão para SQL Server).
	- Clique em **Serviço Biz Talk**


4. A lâmina **Criar Serviço Biz Talk** se abre. Insira um nome para o serviço BizTalk, então clique em **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	A lâmina **Criar Serviço Biz Talk** é fechada e você retorna à lâmina **Criar Conexão Híbrida**.
	
5. Na lâmina Criar Conexão Híbrida, clique em **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Quando o processo for concluído, a área Notificações no portal informa a você que a conexão foi criada com sucesso.
	
	![Success notification][CreateHCSuccessNotification]
	
7. Na lâmina site, o ícone **Conexões Híbridas** agora mostra que uma conexão híbrida foi criada.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>
## D. Instalar o Gerenciador de Conexão Híbrida local para completar a conexão ##

1. Na lâmina site, clique no ícone Conexões Híbridas. 
	
	![Hybrid connections icon][HCIcon]
	
2. Na lâmina **Conexões Híbridas** a coluna **Status** para o ponto de extremidade adicionado recentemente exibe **Não conectado**.Clique na conexão para configurá-la.
	
	![Not connected][NotConnected]
	
	A lâmina Conexão Híbrida se abre.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Na lâmina, clique em **Configuração do Ouvinte**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. A lâmina **Propriedades da Conexão Híbrida** se abre. Em **Gerenciador de Conexão Híbrida Local**, escolha **Clique aqui para instalar**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Na caixa de diálogo de aviso de segurança Executar Aplicativo, selecione **Executar** para continuar.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Na caixa de diálogo **Controle de Conta de Usuário**, selecione **Sim**.
	
	![Choose Yes][UAC]
	
7. O Gerenciador de Conexão Híbrida é baixado e instalado para você. 
	
	![Installing][HCMInstalling]
	
8. Quando a instalação é concluída, clique em **Fechar**.
	
	![Click Close][HCMInstallComplete]
	
	Na lâmina **Conexões Híbridas**, a coluna **Status** agora exibe **Conectado**. 
	
	![Connected Status][HCStatusConnected]

Agora que a infraestrutura de conexão híbrida está concluída, você criará um aplicativo Web que a utilize.

<a name="CreateASPNET"></a>
## E. Criar um projeto Web ASP.NET básico, editar a cadeia de conexão de banco de dados e executar o projeto localmente ##

###Criar um projeto ASP.NET básico###
1. No Visual Studio, no menu **Arquivo**, crie um novo Projeto:
	
	![New Visual Studio project][HCVSNewProject]
	
2. Na seção **Modelos** do diálogo **Novo projeto**, selecione **Web** e escolha **Aplicativo Web ASP.NET** e depois clique em **OK**.
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione **MVC** e, em seguida, clique em **OK**.
	
	![Choose MVC][HCVSChooseMVC]
	
4. Quando o projeto foi criado, a página Leia-me do aplicativo aparece. Não execute o projeto Web ainda.
	
	![Readme page][HCVSReadmePage]

###Edite a cadeia de conexão de banco de dados para o aplicativo###

Nessa etapa, você edita a cadeia de conexão que diz a seu aplicativo onde encontrar seu banco de dados SQL Server Express local. A cadeia de conexão está no arquivo Web.config do aplicativo, que contém informações de configuração para o aplicativo. 

> [WACOM.NOTE] Para assegurar que seu aplicativo utilize o banco de dados que você criou no SQL Server Express, mas não naquele presente no LocalDB padrão do Visual Studio, é importante que você complete essa etapa antes de executar o seu projeto.

1. No Gerenciador de Soluções, clique duas vezes no arquivo Web.config.
	
	![Web.config][HCVSChooseWebConfig]
	
2. Edite a seção **connectionStrings** para que esta direcione ao banco de dados do SQL Server em seu computador local, seguindo a sintaxe utilizada no exemplo a seguir: 
	
	![Connection string][HCVSConnectionString]
	
	Ao compor a cadeia de conexão, mantenha em mente o seguinte:
	
	- Se você está se conectando a uma instância nomeada em vez de conectar-se a uma instância padrão (por exemplo, YourServer\SQLEXPRESS), você precisa configurar seu SQL Server para utilizar portas estáticas. Para obter informações sobre configuração de portas estáticas, consulte [Como configurar um SQL Server para escutar em uma porta específica](http://support.microsoft.com/kb/823938). Por padrão, instâncias nomeadas utilizam UDP e portas dinâmicas, que não têm suporte em Conexões Híbridas. 
	
	- É recomendável que você especifique a porta (1433 por padrão, conforme mostrado no exemplo) na cadeia de conexão, de modo que você possa ter certeza de que seu SQL Server local tem TCP habilitado e utiliza a porta correta. 
	
	- Lembre-se de usar a Autenticação do SQL Server para conectar-se, especificando a ID de usuário e senha em sua cadeia de conexão.
	
3. Clique em **Salvar**, no Visual Studio, para salvar o arquivo Web.config.

###Execute o projeto localmente e registre um novo usuário ###

1. Agora, execute seu novo projeto Web localmente, clicando no botão Procurar em Depurar. Este exemplo usa o Internet Explorer.
	
	![Run project][HCVSRunProject]
	
2. No canto superior direito da página da Web padrão, selecione **Registrar** para registrar uma nova conta:
	
	![Register a new account][HCVSRegisterLocally]
	
3. Digite um nome de usuário e uma senha:
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	Isso cria automaticamente um banco de dados em seu SQL Server local, que contém as informações de associação para seu aplicativo. Uma das tabelas (**dbo.AspNetUsers**) contém credenciais de usuário de site, como aquelas que você acabou de inserir.Você verá essa tabela posteriormente no tutorial.
	
4. Feche a janela de navegador da página da Web padrão. Isso interrompe o aplicativo em Visual Studio.

Agora você está pronto para a próxima etapa, que é publicar o aplicativo no Azure e testá-lo.

<a name="PubNTest"></a>
## F. Publicar o aplicativo Web no Azure e testá-lo ##

Agora, você publicará seu aplicativo em seu site no Azure e o testará, para ver como a conexão híbrida configurada por você anteriormente está sendo usada para conectar seu aplicativo de site ao banco de dados de seu computador local. 

###Publicar o aplicativo Web###

1. Você pode baixar seu perfil de publicação do site no portal do Azure. Na lâmina do seu site, selecione **Baixar perfil de publicação** e então salve o arquivo em seu computador.
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	![Publish profile in downloads folder][HCVSPublishProfileInDownloadsFolder]
	
	Em seguida, você importará esse arquivo em seu aplicativo Web do Visual Studio. 
	
2. No Visual Studio, com o botão direito no nome do projeto no Gerenciador de Soluções e selecione **Publicar**.
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. Na caixa de diálogo **Publicar na Web**, na guia **Perfil**, selecione **Importar**.
	
	![Import][HCVSPublishWebDialogImport]
	
4. Acesse o perfil de publicação baixado por você, selecione-o e então clique em **OK**.
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. Suas informações de publicação são importadas e exibidas na guia **Conexão** da caixa de diálogo. 
	
	![Click Publish][HCVSClickPublish]
	
	Clique em **Publicar**.
	
	Quando a publicação for concluída, seu navegador será inicializado e exibirá seu site, agora familiar - só que, agora, ele vai estar ao vivo na nuvem do Azure!

Em seguida, você utilizará o aplicativo Web online para ver sua Conexão Híbrida em ação.

###Teste o aplicativo Web concluído no Azure ###
1. No canto superior direito de sua página da Web no Azure, selecione **Logon**.
	
	![Test log in][HCTestLogIn]
	
2. Seu site Azure agora está conectado ao banco de dados de associação do seu aplicativo Web, em seu computador local. Para verificar isso, faça logon com as mesmas credenciais que você inseriu anteriormente no banco de dados local.
	
	![Hello greeting][HCTestHelloContoso]
	
3. Para ajudar mais em sua nova conexão híbrida, faça logoff de seu aplicativo Web do Azure e registre-se como outro usuário. Forneça nome de usuário e senha novos e depois clique em **Registrar**.
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. Para confirmar se as credenciais do novo usuário foram armazenadas em seu banco de dados local por meio de sua conexão híbrida, abra o SQL Management Studio em seu computador local. No Gerenciador de Objetos, expanda o banco de dados **MembershipDB** e depois expanda **Tabelas**. Clique com o botão direito na tabela de associação **dbo.AspNetUsers** e escolha **Selecionar as 1000 linhas principais** para exibir os resultados.
	
	![View the results][HCTestSSMSTree]
	
5. Sua tabela de associação local agora exibe ambas as contas - a que você criou localmente, e a que você criou na nuvem do Azure. O que você criou na nuvem foi salvo no seu banco de dados local por meio do recurso de Conexão Híbrida do Azure.
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
Agora você criou e implantou um aplicativo Web ASP.NET que utiliza uma conexão híbrida entre um site na nuvem do Azure e um banco de dados SQL Server local. Parabéns!

##Consulte também##
[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo no canal 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site de Conexões Híbridas](http://azure.microsoft.com/pt-br/services/biztalk-services/)

[Serviços BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](http://azure.microsoft.com/pt-br/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server local por meio de um serviço móvel do Azure utilizando Conexões Híbridas](http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)

[Conectar-se a um SQL Server local por meio de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Visão geral de Identidade ASP.NET](http://www.asp.net/identity)

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!--HONumber=35_1-->
