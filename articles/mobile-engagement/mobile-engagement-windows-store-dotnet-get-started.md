<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para aplicativos universais do Windows"
	description="Saiba como usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos universais do Windows."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para aplicativos universais do Windows

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Universal. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você deve criar um aplicativo do Windows Universal em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação do Windows (WNS).

Este tutorial exige o seguinte:

+ Visual Studio 2013
+ Pacote do Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT]A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos do Windows Universal. Para concluir este tutorial - você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="setup-azme"></a>Configurar o Mobile Engagement para aplicativos do Windows Phone Universal

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica," que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [integração do SDK Universal do Windows para o Mobile Engagement](../mobile-engagement-windows-store-sdk-overview/).

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

###Crie um novo projeto de aplicativo do Windows Universal

As etapas a seguir pressupõem o uso do Visual Studio 2015, embora as etapas sejam semelhantes em versões anteriores do Visual Studio.

1. Inicie o Visual Studio e, na tela **Início**, selecione **Novo Projeto**.

2. No menu pop-up, selecione **Windows 8** -> **Universal** -> **Aplicativo em branco (Windows 8.1 Universal)**. Preencha o **Nome** e o **Nome da solução** do aplicativo, então clique em **OK**.

    ![][1]

> [AZURE.IMPORTANT]O Azure Mobile Engagement ainda não dá suporte a Aplicativos Universais do Windows do Windows 10.

Você criou um novo projeto Aplicativo Universal do Windows no qual integraremos o SDK do Mobile Engagement do Azure.

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Instale o pacote do nuget [MicrosoftAzure.MobileEngagement] em seu projeto. Se estiver visando as plataformas Windows e Windows Phone, você precisará fazer isso em ambos os projetos. O mesmo pacote Nuget coloca os binários corretos específicos de plataforma em cada projeto.

2. Abra **Package.appxmanifest** e certifique-se de que a funcionalidade a seguir é adicionada lá:

		Internet (Client)

	![][2]

3. Agora, copie a cadeia de conexão que você copiou anteriormente para o seu Aplicativo Mobile Engagement e cole-o no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:

	![][3]

	>[AZURE.TIP]Se seu aplicativo irá usar como destino ambas as plataformas Windows e Windows Phone, você deve ainda criar dois aplicativos do Mobile Engagement - um para cada plataforma com suporte. Isso é para garantir que você possa criar segmentação correta do público-alvo e possa enviar notificações apropriadamente direcionadas a cada plataforma.

4. No arquivo `App.xaml.cs`:

	a. Adicione a instrução `using`:

			using Microsoft.Azure.Engagement;

	b. Inicialize o SDK no método **OnLaunched**:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Insira o seguinte no método **OnActivated** e adicione o método se ele não estiver presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. 	Em **MainPage.xaml.cs**, adicione a instrução `using`:

		using Microsoft.Azure.Engagement;

2. Substitua a classe base de **MainPage**, de **Page** para **EngagementPage**:

		class MainPage : EngagementPage

3. No arquivo `MainPage.xaml`:

	a. Adicione às suas declarações de namespaces:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Substitua **Page** no nome da marca XML por **engagement:EngagementPage**.
	
> [AZURE.IMPORTANT]Se sua página substitui o método `OnNavigatedTo`, certifique-se de chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada (a `EngagementPage` chama `StartActivity` dentro de seu método `OnNavigatedTo`). Isso é especialmente importante em um projeto do Windows Phone, no qual o modelo padrão tem um método `OnNavigatedTo`.

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite interagir e entrar em contato com usuários com notificações por push e mensagens no aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

###Habilitar o aplicativo para receber Notificações por Push do WNS

1. No arquivo `Package.appxmanifest`, na guia **Aplicativo**, em **Notificações**, defina **Compatível com Toast:** como **Sim**

	![][5]

###Inicializar o SDK do REACH

1. Em `App.xaml.cs`, chame **EngagementReach.Instance.Init();** na função **OnLaunched** logo depois da inicialização do agente:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. Em `App.xaml.cs`, chame **EngagementReach.Instance.Init(e);** na função **OnActivated** logo depois da inicialização do agente:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Você estará pronto para enviar uma notificação do sistema. Agora vamos verificar se você realizou corretamente essa integração básica.

###Conceder acesso ao Mobile Engagement para enviar notificações

1. Você terá que associar seu aplicativo a um Aplicativo da Windows Store para obter seu **SID (Identificador de Segurança de Pacote)** e sua **Chave Secreta** (Segredo do Cliente). Você pode criar um aplicativo no [Centro de Desenvolvimento da Windows Store] e se certificar de usar **Associar o Aplicativo à Store** do Visual Studio.

2. Navegue até **Configurações**, no portal do Mobile Engagement, e clique na seção **Push Nativo** à esquerda.

3. Clique no botão **Editar** para inserir seu **SID (Identificador de Segurança de Pacote)** e sua **Chave Secreta**, conforme mostrado abaixo:

	![][6]

##<a id="send"></a>Envie uma notificação para seu aplicativo

[AZURE.INCLUDE [Criar campanha de Push do Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Agora você verá uma notificação da sua campanha em seu dispositivo — o aplicativo deve ser fechado para ver essa notificação. Se o aplicativo estava em execução, certifique-se de que ele tenha sido fechado alguns minutos antes de ativar a campanha para poder receber a notificação do sistema. Se você deseja integrar a notificação no aplicativo para que a notificação apareça no aplicativo quando ele for aberto, consulte [Aplicativos do Windows Universal - Integração de sobreposição].

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centro de Desenvolvimento da Windows Store]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Aplicativos do Windows Universal - Integração de sobreposição]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png

<!---HONumber=Sept15_HO4-->