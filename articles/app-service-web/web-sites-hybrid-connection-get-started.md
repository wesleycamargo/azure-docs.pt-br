<properties 
	pageTitle="Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure" 
	description="Criar uma conexão entre um aplicativo Web no Serviço de Aplicativo do Azure e um recurso local que usa uma porta TCP estática" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="cephalin"/>

#Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure

Você pode conectar um aplicativo Web no Serviço de Aplicativo do Azure a qualquer recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs Web HTTP, Serviços Móveis e os serviços Web mais personalizados. Este artigo mostra como criar uma conexão híbrida entre um aplicativo Web no Serviço de Aplicativo e um banco de dados do SQL Server local.

> [AZURE.NOTE]A parte de aplicativos Web do recurso de conexões híbridas está disponível apenas no [Portal do Azure](https://portal.azure.com). Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Pré-requisitos
- Uma assinatura do Azure. Para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. É recomendado usar uma instância padrão no SQL Server porque ele usa a porta estática 1433. Para informações sobre a instalação e a configuração do SQL Server Express para uso com conexões híbridas, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- O computador no qual você instala o agente do Hybrid Connection Manager local, descrito posteriormente neste artigo:

	- Precisa ser capaz de conectar-se ao Azure pela porta 5671
	- Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local. 

> [AZURE.NOTE]As etapas neste artigo pressupõem que você está utilizando o navegador a partir do computador que hospedará o agente local de conexão híbrida.


## Criar um aplicativo Web no Portal do Azure ##

> [AZURE.NOTE]Se já criou no Portal do Azure um aplicativo Web que deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC) e começar de lá.

1. No canto superior esquerdo do [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** > **Aplicativo Web**.
	
	![Novo aplicativo Web][NewWebsite]
	
2. Na folha **Aplicativo Web**, forneça uma URL e clique em **Criar**.
	
	![Nome do site][WebsiteCreationBlade]
	
3. Após alguns momentos, o aplicativo Web é criado e sua folha de aplicativo Web aparece. A lâmina é um painel com rolagem vertical, que permite a você gerenciar o seu site.
	
	![Website executando][WebSiteRunningBlade]
	
4. Para verificar se o site está funcionando, você pode clicar no ícone **Procurar** para exibir a página padrão.
	
	![Clique em Procurar para ver seu aplicativo Web][Browse]
	
	![Página de aplicativo Web padrão][DefaultWebSitePage]
	
Em seguida, você criará uma conexão híbrida e um serviço do BizTalk para o aplicativo Web.

<a name="CreateHC"></a>
## Criar uma Conexão Híbrida e um Serviço do BizTalk ##

1. Na folha do seu aplicativo Web, clique em **Todas as configurações** > **Rede** > **Configurar seus pontos de extremidade de conexão híbrida**.
	
	![Conexões Híbridas][CreateHCHCIcon]
	
2. Na lâmina Conexões Híbridas, clique em **Adicionar**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. A lâmina **Adicionar uma conexão híbrida** se abre. Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a lâmina **Criar Conexão Híbrida** se abre para você.
	
	![Criar uma Conexão Híbrida][TwinCreateHCBlades]
	
	Na **folha Criar conexão híbrida**:
	- Para **Nome**, forneça um nome para a conexão.
	- Para **Nome de host**, digite o nome do computador local que hospeda o recurso.
	- Para **Porta**, digite o número da porta que o recurso local usa (1433 para uma instância do SQL Server padrão).
	- Clique em **Serviço BizTalk**


4. A folha **Criar serviço BizTalk** será aberta. Insira um nome para o serviço BizTalk, então clique em **OK**.
	
	![Criar Serviço BizTalk][CreateHCCreateBTS]
	
	A folha **Criar Serviço BizTalk** é fechada e você retorna à folha **Criar Conexão Híbrida**.
	
5. Na lâmina Criar Conexão Híbrida, clique em **OK**.
	
	![Clique em OK][CreateBTScomplete]
	
6. Quando o processo for concluído, a área de notificações no Portal informa a você que a conexão foi criada com sucesso.
	<!-- TODO

    Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the classic portal
	(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
	Create hybrid conn step, you get the following error
	Failed to create hybrid connection RelecIoudHC. The 
	resource type could not be found in the namespace 
	'Microsoft.BizTaIkServices for api version 2014-06-01'.
	
	The error indicates it couldn't find the type, not the instance.
	![Success notification][CreateHCSuccessNotification]
	-->
7. Na folha do aplicativo Web, o ícone **Conexões híbridas** agora mostra que uma conexão híbrida foi criada.
	
	![Uma conexão híbrida criada][CreateHCOneConnectionCreated]
	
Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>
## Instalar o Gerenciador de Conexões Híbridas local para completar a conexão ##

1. Na folha do aplicativo Web, clique em **Todas as configurações** > **Rede** > **Configurar seus pontos de extremidade de conexão híbrida**. 
	
	![Ícone Conexões Híbridas][HCIcon]
	
2. Na lâmina **Conexões Híbridas** a coluna **Status** para o ponto de extremidade adicionado recentemente exibe **Não conectado**. Clique na conexão para configurá-la.
	
	![Não conectado][NotConnected]
	
	A lâmina Conexão Híbrida se abre.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Na lâmina, clique em **Configuração do Ouvinte**.
	
	![Clique em Configuração do Ouvinte][ClickListenerSetup]
	
4. A lâmina **Propriedades da Conexão Híbrida** se abre. Em **Gerenciador de Conexão Híbrida Local**, escolha **Clique aqui para instalar**.
	
	![Clique aqui para instalar][ClickToInstallHCM]
	
5. Na caixa de diálogo de aviso de segurança Executar Aplicativo, selecione **Executar** para continuar.
	
	![Selecione Executar para continuar][ApplicationRunWarning]
	
6.	Na caixa de diálogo **Controle de Conta de Usuário**, selecione **Sim**.
	
	![Selecione Sim][UAC]
	
7. O Gerenciador de Conexão Híbrida é baixado e instalado para você.
	
	![Instalando][HCMInstalling]
	
8. Quando a instalação é concluída, clique em **Fechar**.
	
	![Clique em Fechar][HCMInstallComplete]
	
	Na lâmina **Conexões Híbridas**, a coluna **Status** agora exibe **Conectado**.
	
	![Status Conectado][HCStatusConnected]

Agora que a infraestrutura de conexão híbrida está concluída, você criará um aplicativo híbrido que a utilize.

>[AZURE.NOTE]Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<a name="NextSteps"></a>
## Próximas etapas ##

- Para obter informações sobre como criar um aplicativo Web ASP.NET que utiliza uma conexão híbrida, consulte [Conectar-se a um SQL Server local a partir de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- Para obter informações sobre como usar uma conexão híbrida com um serviço móvel, consulte [Conectar-se a um SQL Server local a partir de um serviço móvel do Azure utilizando Conexões Híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Recursos adicionais

[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo no Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site de Conexões Híbridas](http://azure.microsoft.com/services/biztalk-services/)

[Serviços BizTalk: guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](../biztalk-dashboard-monitor-scale-tabs/)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server local a partir de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=AcomDC_1203_2015-->
