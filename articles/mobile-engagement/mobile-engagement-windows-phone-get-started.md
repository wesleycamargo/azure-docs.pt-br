<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para aplicativos do Windows Phone Silverlight"
	description="Saiba como usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos do Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para aplicativos do Windows Phone Silverlight

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Phone Silverlight. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo do Windows Phone Silverlight em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação por Push da Microsoft (MPNS).

> [AZURE.NOTE] Se você estiver almejando o Windows Phone 8.1 (não Silverlight), consulte o [tutorial do Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial exige o seguinte:

+ Visual Studio 2013
+ Pacote do Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configurar o Mobile Engagement para aplicativos do Windows Phone

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [integração do SDK do Windows Phone para o Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md).

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

###Criação de um novo projeto do Windows Phone Silverlight

As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio.

1. Inicie o Visual Studio e, na tela **Início**, selecione **Novo Projeto**.

2. No pop-up, selecione **Windows 8** -> **Windows Phone** -> **Aplicativo em Branco (Silverlight para Windows Phone)**. Preencha o **Nome** e o **Nome da solução** do aplicativo, então clique em **OK**.

    ![][1]

3. Você pode escolher como destino **Windows Phone 8.0** ou **Windows Phone 8.1**.

Você agora criou um novo aplicativo do Windows Phone Silverlight no qual integraremos o SDK do Mobile Engagement do Azure.

###Conectar o aplicativo ao back-end do Mobile Engagement

1. Instale o pacote do nuget [MicrosoftAzure.MobileEngagement] em seu projeto.

2. Abra `WMAppManifest.xml` (na pasta Propriedades) e verifique se os itens a seguir estão declarados (adicione caso não estejam) na marca `<Capabilities />`:

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Agora cole a cadeia de conexão que você copiou anteriormente para o seu aplicativo do Mobile Engagement e cole no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:

    ![][3]

4. No arquivo `App.xaml.cs`:

	a. Adicione a instrução `using`:

			using Microsoft.Azure.Engagement;

	b. Inicializar o SDK no método `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Insira o seguinte em `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Em MainPage.xaml.cs, adicione a instrução `using`:

    	using Microsoft.Azure.Engagement;

2. Substitua a classe base de **MainPage**, que é anterior a **PhoneApplicationPage**, por **EngagementPage**.

    	class MainPage : EngagementPage 
	
3. No arquivo `MainPage.xml`:

	a. Adicione às suas declarações de namespaces:

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Substitua `phone:PhoneApplicationPage` no nome da marca XML por `engagement:EngagementPage`.

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite interagir e entrar em contato com usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

###Habilitar o aplicativo para receber Notificações por Push do MPNS

Adicionar novos recursos ao arquivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Inicializar o SDK do REACH

1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função **Application\_Launching** logo após a inicialização do agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Em `App.xaml.cs`, chame `EngagementReach.Instance.OnActivated(e);` na função **Application\_Activated**, logo após a inicialização do agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Você está pronto. Agora vamos verificar se você criou corretamente essa integração básica.

##<a id="send"></a>Envie uma notificação para seu aplicativo

[AZURE.INCLUDE [Criar campanha de Push do Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Você agora verá uma notificação em seu dispositivo que será exibida como uma notificação no aplicativo, se o aplicativo estiver aberto como uma notificação do sistema semelhante à seguinte:

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=AcomDC_0504_2016-->