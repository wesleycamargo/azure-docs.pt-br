<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para aplicativos do Windows Universal"
	description="Aprenda a usar o Mobile Engagement do Azure com Análises e Notificações por Push para Aplicativos do Windows Universal."
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
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para aplicativos do Windows Universal

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Universal. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você deve criar um aplicativo do Windows Universal em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação do Windows (WNS). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos do destino com base em suas propriedades dos dispositivos. Siga o tutorial a seguir para aprender a usar o Mobile Engagement para endereçar usuários e grupos de dispositivos específicos.

Este tutorial exige o seguinte:

+ Visual Studio 2013
+ O [SDK do Windows Universal para o Mobile Engagement]

> [AZURE.IMPORTANT]A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos do Windows Universal. Para concluir este tutorial - você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="setup-azme"></a>Introdução ao Mobile Engagement para aplicativos do Windows Phone Universal

1. Faça logon no Portal de Gerenciamento do Azure e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, **Mobile Engagement**, então, em **Criar**.

   	![][7]

3. No pop-up exibido, insira as seguintes informações:

   	![][8]

	- **Nome do Aplicativo**: digite o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
	- **Plataforma**: selecione a plataforma de destino (**Windows Universal**) para o aplicativo (se seu aplicativo for destinado para várias plataformas, repita esse tutorial para cada uma).
	- **Nome do Recurso do Aplicativo**: é o nome pelo qual o aplicativo poderá ser acessado via APIs e URLs. Você só deve usar caracteres URL convencionais. O nome gerado automaticamente deve fornecer uma base sólida. Você também deve acrescentar o nome da plataforma para evitar qualquer conflito de nomes, pois o nome deve ser exclusivo.
	- **Local**: selecione o datacenter no qual o aplicativo (e o mais importante, sua Coleção) será hospedado.
	- **Coleção**: se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente, caso contrário, selecione a Nova Coleção.
	- **Nome da Coleção**: isso representa seu grupo de aplicativos. Isso garantirá que todos os seus aplicativos estejam em um grupo que permitirá cálculos agregados das métricas. Você deve usar o nome da empresa ou departamento aqui, se aplicável.

	> [AZURE.TIP]Se seu aplicativo Universal irá usar como destino ambas as plataformas Windows e Windows Phone, você deve ainda criar dois aplicativos do Mobile Engagement, um para cada plataforma com suporte. Isso é para garantir que você pode criar uma segmentação adequada do público e é capaz de enviar notificações de destino apropriadamente para cada plataforma.

4. Selecione o aplicativo que você acabou de criar na guia **Aplicativos**.

5. Clique em **Informações da Conexão** para exibir as configurações da conexão para colocar em sua integração SDK no aplicativo móvel.

   	![][10]

6. Cópia da **Cadeia de Conexão** - isso é o que você precisará para identificar esse aplicativo no código do aplicativo e conectar o Mobile Engagement a partir de seu aplicativo do Universal.

   	![][11]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica," que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [documentação do SDK do Windows Universal para o Mobile Engagement].

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

###Crie um novo projeto de aplicativo do Windows Universal

Você pode ignorar esta etapa se você já tiver um aplicativo e estiver familiarizado com o desenvolvimento do Windows Universal.

1. Inicie o Visual Studio e, na tela inicial, selecione **Novo Projeto...**.

2. Na janela pop-up, selecione `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`. Preencha o aplicativo `Name` e `Solution name` e, em seguida, clique em **OK**.

   	![][13]

Você criou um novo aplicativo do Windows Universal no qual integraremos o SDK do Mobile Engagement do Azure.

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Instalação do pacote nuget do [SDK do Windows Universal do Mobile Engagement] em seu projeto. Se você estiver selecionando ambas as plataformas Windows e Windows Phone para direcionamento você precisará fazer isso para os dois projetos. O mesmo pacote do Nuget colocará os binários específicos da plataforma correta em cada projeto.

2. Abra `Package.appxmanifest` e adicione o seguinte se ele não for adicionado automaticamente:

		Internet (Client)

	![][20]

3. Agora cole a cadeia de conexão que você copiou anteriormente para o seu aplicativo do Mobile Engagement e cole-o no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:

	![][22]

	>[AZURE.TIP]Se seu aplicativo irá usar como destino ambas as plataformas Windows e Windows Phone, você deve ainda criar dois aplicativos do Mobile Engagement - um para cada plataforma com suporte. Isso é para garantir que você pode criar uma segmentação adequada do público e é capaz de enviar notificações de destino apropriadamente para cada plataforma.

4. No arquivo `App.xaml.cs`:

	a. Adicione a instrução `using`:

			using Microsoft.Azure.Engagement;

	b. Inicializar o SDK no método `OnLaunched`:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Insira o seguinte no método `OnActivated` e adicione o método se ele não ainda estiver presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement. Vamos fazer isso ao subclassificar nosso `MainPage` com o `EngagementPage` que o SDK do Mobile Engagement fornece.

1. 	Adicione a instrução `using`:

		using Microsoft.Azure.Engagement;

2. Substitua a superclasse de `MainPage`, que fica antes de `Page`, por `EngagementPage`:

	![][23]

3. No arquivo `MainPage.xml`:

	a. Adicione às suas declarações de namespaces:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Substitua o `Page` no nome da marca xml por `engagement:EngagementPage`

###Verifique se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo conecta o back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement.

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior.

	![][26]

2. Você será levado para a página de configurações do seu Portal do Engagement para seu aplicativo. A partir disso, clique na guia **Monitor** conforme mostrado abaixo. ![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo.

4. No Visual Studio, inicie o aplicativo no simulador ou em um dispositivo conectado.

5. Se tiver funcionado, agora você deverá ver uma sessão no monitor! ![][33]

**Parabéns!** Você concluiu com êxito a primeira etapa deste tutorial com um aplicativo que se conecta ao back-end do Mobile Engagement, que já está enviando dados.

##<a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo

O Mobile Engagement permite a você interagir e entrar em contato com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir irão configurar seu aplicativo para recebê-las.

###Habilitar o aplicativo para receber Notificações por Push do WNS

1. No arquivo `Package.appxmanifest`, na guia `Application`, em `Notifications`, selecione `Yes` para `Toast capable:`:

	![][35]

###Inicializar o SDK do REACH

1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função `OnLaunched` logo após a inicialização do agente:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init(e);` na função `OnActivated` logo após a inicialização do agente:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Você estará pronto para enviar uma Notificação do sistema, agora, verificaremos se você realizou corretamente esta integração básica.

###Conceder acesso ao Mobile Engagement para enviar Notificações

1. Você precisará associar seu aplicativo com um aplicativo da Windows Store para obter seu `Package security identifier (SID)` e sua `Secret Key` (Segredo do cliente). Você pode criar um aplicativo a partir do [Centro de desenvolvimento da Windows Store] e, em seguida, certifique-se de **Associar o aplicativo com a Store** do Visual Studio.

2. Navegue até as **Configurações** do seu Portal do Mobile Engagement e clique na seção `Native Push` à esquerda.

3. Clique no botão `Edit` para inserir o `Package security identifier (SID)` e o `Secret Key` conforme mostrado abaixo:

	![][36]

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora, criaremos uma campanha simples de Notificação por Push que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** em seu portal do Mobile Engagement

2. Clique em **Novo comunicado** para criar sua campanha de push. ![][37]

3. Configurar o primeiro campo da campanha por meio das seguintes etapas: ![][38]

	a. Nome de sua campanha.

	b. Selecione a **Hora de entrega** como *Sempre* para permitir que o aplicativo receba uma notificação quer o aplicativo tenha sido iniciado ou não.

	c. No texto de notificação - digite o **Título** que estará em negrito no envio por push.

	d. Depois digite sua mensagem.

4. Role para baixo e, na seção de conteúdo, selecione **Notificação apenas**. ![][39]

5. Você terminou a configuração da campanha mais básica possível, agora role para baixo novamente e clique em **Criar** para salvar a sua campanha.

6. Última etapa, clique em **Ativar** para ativar sua campanha e enviar notificações por push. ![][41]

Agora você verá uma notificação da sua campanha em seu dispositivo — o aplicativo deve ser fechado para ver essa notificação. Se o aplicativo estiver em execução, certifique-se que ele está fechado por alguns minutos antes de ativar a campanha para poder receber notificação do sistema. Se você deseja integrar a notificação no aplicativo para que a notificação apareça no aplicativo quando ele for aberto, consulte [Aplicativos do Windows Universal - Integração de sobreposição].

<!-- URLs. -->
[documentação do SDK do Windows Universal para o Mobile Engagement]: ../mobile-engagement-windows-store-integrate-engagement/
[SDK do Windows Universal do Mobile Engagement]: http://go.microsoft.com/?linkid=9864592
[SDK do Windows Universal para o Mobile Engagement]: http://go.microsoft.com/?linkid=9864592
[Centro de desenvolvimento da Windows Store]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Aplicativos do Windows Universal - Integração de sobreposição]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png
 

<!---HONumber=July15_HO2-->