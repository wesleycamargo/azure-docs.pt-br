<properties
	pageTitle="Introdução ao Azure Mobile Engagement para os Aplicativos Web | Microsoft Azure"
	description="Saiba como usar o Azure Mobile Engagement com a análise e as notificações por push para os Aplicativos Web."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="js"
	ms.topic="hero-article"
	ms.date="06/01/2016"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para Aplicativos Web

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo Web.

Este tutorial exige o seguinte:

+ Visual Studio 2015 ou qualquer outro editor de sua escolha
+ [SDK da Web](http://aka.ms/P7b453) 

Esse SDK da Web está na Visualização, só dá suporte à Análise no momento e ainda não suporta enviar notificações por push no aplicativo ou no navegador.

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##Configurar o Mobile Engagement para seu aplicativo Web

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica", que é o conjunto mínimo exigido para coletar dados.

Criaremos um aplicativo Web básico com o Visual Studio para demonstrar a integração, embora você possa seguir as etapas com qualquer aplicativo Web criado fora do Visual Studio também.

###Criar um novo aplicativo Web

As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio.

1. Inicie o Visual Studio e, na tela **Início**, selecione **Novo Projeto**.

2. No menu pop-up, selecione **Web** -> **Aplicativo Web do ASP.Net**. Preencha o **Nome** do aplicativo, **Local** e **Nome da solução**, em seguida, clique em **OK**.

3. No pop-up **Selecionar um modelo**, selecione **Vazio** em **Modelos do ASP.Net 4.5** e clique em **OK**.

Agora, você criou um novo projeto de Aplicativo Web em branco no qual integraremos o SDK do Azure Mobile Engagement da Web.

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Crie uma nova pasta denominada **javascript** em sua solução e adicione o arquivo JS SDK da Web **azure-engagement.js** a ela. 

2. Adicione um novo arquivo denominado **main.js** a essa pasta javascript com o código a seguir. Atualize a cadeia de conexão. Este objeto `azureEngagement` será usado para acessar os métodos SDK da Web.

		var azureEngagement = {
		    debug: true,
		    connectionString: 'xxxxx'
		};

	![Visual Studio com arquivos js][1]

##Habilitar o monitoramento em tempo real

Para começar a enviar dados e assegurar que os usuários estejam ativos, você deve enviar, pelo menos, uma Atividade para o back-end do Mobile Engagement. Uma atividade no contexto de um aplicativo Web é uma página da Web.

1. Crie uma nova página denominada **home.html** em sua solução e configure-a como a página inicial de seu aplicativo Web. 
2. Inclua os dois javascripts adicionados anteriormente nesta página acrescentando o seguinte na marcação body. 

	    <script type="text/javascript" src="javascript/main.js"></script>
	    <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Atualize a marcação body para chamar o método `startActivity` do EngagementAgent
		
		<body onload="engagement.agent.startActivity('Home')">

4. Sua **home.html** ficará assim
		
		<html>
		<head>
			...
		</head>
		<body onload="engagement.agent.startActivity('Home')">
		    <script type="text/javascript" src="javascript/main.js"></script>
		    <script type="text/javascript" src="javascript/azure-engagement.js"></script>
		</body>
		</html>

##Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##Estender a análise

Aqui estão todos os métodos disponíveis no momento com o SDK da Web que você pode usar para a análise:

1. Páginas de atividades/Web:

		engagement.agent.startActivity(name);
		engagement.agent.endActivity();

2. Eventos
		
		engagement.agent.sendEvent(name, extras);

3. Erros

		engagement.agent.sendError(name, extras);

4. Trabalhos

		engagement.agent.startJob(name);
		engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

<!---HONumber=AcomDC_0615_2016-->