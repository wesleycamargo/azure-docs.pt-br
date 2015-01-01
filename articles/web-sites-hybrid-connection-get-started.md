<properties title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Conexão híbrida: Conectar um site do Azure a um recurso local" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Conectar um site do Azure a um recurso local usando Conexões Híbridas

Você pode conectar um site no Microsoft Azure a qualquer recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, Serviços Móveis e os serviços Web mais personalizados. Esse artigo mostra como você pode criar uma conexão híbrida entre um site do Azure e um banco de dados local de SQL Server.

> [WACOM.NOTE] A porção Sites do recurso Conexões Híbridas está disponível somente no [portal Visualização do Azure](https://portal.azure.com). Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Pré-requisitos
- Uma assinatura do Azure. Para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pt-br/pricing/free-trial/). 

- Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. É recomendado usar uma instância padrão no SQL Server porque ele usa a porta estática 1433. Para informações sobre a instalação e a configuração do SQL Server Express para uso com conexões híbridas, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- O computador no qual você instala o agente do Hybrid Connection Manager local, descrito posteriormente neste artigo:

	- Precisa ser capaz de conectar-se ao Azure pela porta 5671
	- Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local. 

> [WACOM.NOTE] As etapas neste artigo pressupõem que você está utilizando o navegador no computador que hospedará o agente local de conexão híbrida.


##Neste artigo##


[Criar um site no Portal de Visualização do Azure](#CreateSite)

[Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC)

[Instalar o Hybrid Connection Manager local para concluir a conexão](#InstallHCM)

[Próximas etapas](#NextSteps)


## Criar um site no Portal de Visualização do Azure ##

> [WACOM.NOTE] Se você já criou um site no Portal de Visualização do Azure que você deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC) e começar dali.

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
## Criar uma Conexão Híbrida e um Serviço do BizTalk ##

1. De volta ao Portal de Visualização, role para baixo pela lâmina para seu site e selecione **Conexões Híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Na lâmina Conexões Híbridas, clique em **Adicionar**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. A lâmina **Adicionar uma conexão híbrida** se abre.  Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a lâmina **Criar Conexão Híbrida** se abre para você.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Na lâmina **Criar Conexão Híbrida**:
	- Como **Nome**, forneça um nome para a conexão.
	- Como **Nome de host**, insira o nome do computador local que hospeda seu recurso.
	- Como **Porta**, insira o número da porta que o seu recurso local utiliza (1433 para uma instância padrão de SQL Server).
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
## Instalar o Gerenciador de Conexão Híbrida local para completar a conexão ##

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

Agora que a infraestrutura de conexão híbrida está concluída, você criará um aplicativo híbrido que a utilize. 

<a name="NextSteps"></a>
## Próximas Etapas ##

- Para obter informações sobre como criar um aplicativo Web ASP.NET que utiliza uma conexão híbrida, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- Para obter informações sobre como usar uma conexão híbrida com um serviço móvel, consulte [Conectar-se a um SQL Server local por meio de um serviço móvel do Azure utilizando Conexões Híbridas](http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Recursos adicionais

[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo no canal 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site de Conexões Híbridas](http://azure.microsoft.com/pt-br/services/biztalk-services/)

[Serviços BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](http://azure.microsoft.com/pt-br/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server local por meio de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=35_1-->
