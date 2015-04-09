<properties 
	pageTitle="Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure" 
	description="Criar uma conexão entre um aplicativo Web no Serviço de Aplicativo do Azure e um recurso local que usa uma porta TCP estática" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure

Você pode conectar um aplicativo Web no Serviço de Aplicativo do Azure a qualquer recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs Web HTTP, Serviços Móveis e os serviços Web mais personalizados. Este artigo mostra como criar uma conexão híbrida entre um aplicativo Web no Serviço de Aplicativo e um banco de dados do SQL Server local.

> [AZURE.NOTE] A parte de aplicativos Web do recurso de conexões híbridas está disponível apenas no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## Pré-requisitos
- Uma assinatura do Azure. Para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. É recomendado usar uma instância padrão no SQL Server porque ele usa a porta estática 1433. Para informações sobre a instalação e a configuração do SQL Server Express para uso com conexões híbridas, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- O computador no qual você instala o agente do Hybrid Connection Manager local, descrito posteriormente neste artigo:

	- Precisa ser capaz de conectar-se ao Azure pela porta 5671
	- Deve ser capaz de atingir o *hostname*:*portnumber* do recurso local. 

> [AZURE.NOTE] As etapas neste artigo pressupõem que você está utilizando o navegador no computador que hospedará o agente local de conexão híbrida.


## Criar um aplicativo Web no Portal do Azure ##

> [AZURE.NOTE] Se você já criou no Portal do Azure um aplicativo Web que você deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC) e começar dali.

1. No canto inferior esquerdo do [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** > **Site**.
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. Na lâmina **Aplicativo Web**, forneça uma URL > **Criar**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Após alguns momentos, o aplicativo Web é criado e sua lâmina de aplicativo Web aparece. A lâmina é um painel com rolagem vertical, que permite a você gerenciar o seu site.
	
	![Website running][WebSiteRunningBlade]
	
4. Para verificar se o site está funcionando, você pode clicar no ícone **Procurar** para exibir a página padrão.
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
Em seguida, você criará uma conexão híbrida e um serviço do BizTalk para o aplicativo Web.

<a name="CreateHC"></a>
## Criar uma Conexão Híbrida e um Serviço do BizTalk ##

1. Role para baixo na lâmina de seu aplicativo Web e escolha **Conexões Híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Na lâmina Conexões Híbridas, clique em **Adicionar**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. A lâmina **Adicionar uma conexão híbrida** se abre.  Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a lâmina **Criar Conexão Híbrida** se abre para você.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Na lâmina **Criar Conexão Híbrida**:
	- Como **Nome**, forneça um nome para a conexão.
	- Como **Nome de host**, insira o nome do computador local que hospeda seu recurso.
	- Como **Porta**, insira o número da porta que o seu recurso local utiliza (1433 para uma instância padrão de SQL Server).
	- Clique em **Serviço Biz Talk**


4. A lâmina **Criar serviço BizTalk** será aberta. Insira um nome para o serviço BizTalk, então clique em **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	A lâmina **Criar Serviço BizTalk** é fechada e você retorna à lâmina **Criar Conexão Híbrida**.
	
5. Na lâmina Criar Conexão Híbrida, clique em **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Quando o processo for concluído, a área Notificações no portal informa a você que a conexão foi criada com sucesso.
	<!-- TODO

    Tudo falha nesta etapa. Não consigo criar um serviço BizTalk no portal dogfood. Alterno para o portal antigo
	(portal completo) e o serviço BizTalk foi criado, mas ele não parece permitir a você conectá-los - Quando você terminar a
	etapa Criar conexão híbrida, você obtém o seguinte erro:
	Falha ao criar a conexão híbrida RelecIoudHC. O 
	tipo de recurso não pôde ser encontrado no namespace 
	'Microsoft.BizTaIkServices para API versão 01-06-2014'.
	
	O erro indica que ele não foi capaz de encontrar o tipo, não se trata da instância.
	![Success notification][CreateHCSuccessNotification]
	-->
7. Na lâmina do aplicativo Web, o ícone **Conexões Híbridas** agora mostra que uma conexão híbrida foi criada.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>
## Instalar o Gerenciador de Conexão Híbrida local para completar a conexão ##

1. Na lâmina aplicativo Web, clique no ícone Conexões Híbridas. 
	
	![Hybrid connections icon][HCIcon]
	
2. Na lâmina **Conexões Híbridas**, a coluna **Status** para o ponto de extremidade adicionado recentemente exibe **Não conectado**. Clique na conexão para configurá-la.
	
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

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<a name="NextSteps"></a>
## Próximas Etapas ##

- Para obter informações sobre como criar um aplicativo Web ASP .NET que utiliza uma conexão híbrida, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- Para obter informações sobre como usar uma conexão híbrida com um serviço móvel, consulte [Conectar-se a um SQL Server local por meio de um serviço móvel do Azure utilizando Conexões Híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Recursos adicionais

[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo no canal 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site de Conexões Híbridas](http://azure.microsoft.com/services/biztalk-services/)

[Serviços BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](../biztalk-dashboard-monitor-scale-tabs/)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server local por meio de Serviços Móveis do Azure utilizando Conexões Híbridas (vídeo no canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência em sites e aplicativos Web no Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=49-->