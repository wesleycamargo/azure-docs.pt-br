<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para aplicativos do Windows Phone Silverlight"
	description="Saiba como usar o Mobile Engagement do Azure com Análises e Notificações por Push para Aplicativos do Windows Phone Silverlight."
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
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para aplicativos do Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados de um aplicativo do Windows Phone Silverlight. Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo do Windows Phone Silverlight em branco que coleta dados básicos e recebe notificações por push usando o Serviço de Notificação por Push da Microsoft (MPNS). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos do destino com base em suas propriedades dos dispositivos (usando o MPNS). Siga o tutorial a seguir para aprender a usar o Mobile Engagement para endereçar usuários e grupos de dispositivos específicos.

> [AZURE.NOTE]Se você estiver selecionando o Windows Phone 8.1 (sem Silverlight), então consulte o [tutorial do Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial exige o seguinte:

+ Visual Studio 2013
+ O [SDK do Windows Phone para o Mobile Engagement]

> [AZURE.IMPORTANT]Concluir este tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos do Windows Phone Silverlight e para concluí-lo, você deve ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="setup-azme"></a>Introdução ao Mobile Engagement para aplicativos do Windows Phone Silverlight

1. Faça logon no Portal de Gerenciamento do Azure e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, **Mobile Engagement**, então, em **Criar**.

   	![][7]

3. No pop-up exibido, insira as seguintes informações:

   	![][8]

	- **Nome do Aplicativo**: digite o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
	- **Plataforma**: selecione a plataforma de destino (\*\*Windows Phone Silverlight\*\*) para o aplicativo (se seu aplicativo for destinado para várias plataformas, repita esse tutorial para cada uma).
	- **Nome do Recurso do Aplicativo**: é o nome pelo qual o aplicativo poderá ser acessado via APIs e URLs. Você só deve usar caracteres URL convencionais. O nome gerado automaticamente deve fornecer uma base sólida. Você também deve acrescentar o nome da plataforma para evitar qualquer conflito de nomes, pois o nome deve ser exclusivo.
	- **Local**: selecione o datacenter no qual o aplicativo (e o mais importante, sua Coleção) será hospedado.
	- **Coleção**: se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente, caso contrário, selecione a Nova Coleção.
	- **Nome da Coleção**: isso representa seu grupo de aplicativos. Isso garantirá que todos os seus aplicativos estejam em um grupo que permitirá cálculos agregados das métricas. Você deve usar o nome da empresa ou departamento aqui, se aplicável.

4. Selecione o aplicativo que você acabou de criar na guia **Aplicativos**.

5. Clique em **Informações da Conexão** para exibir as configurações da conexão para colocar em sua integração SDK no aplicativo móvel.

   	![][10]

6. Cópia da **Cadeia de Conexão** - isso é o que você precisará para identificar esse aplicativo no Código do aplicativo e conectar o Mobile Engagement a partir de seu Aplicativo para Telefone.

   	![][11]

##<a id="connecting-app"></a>Conectando seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica," que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na documentação do [SDK do Windows Phone para o Mobile Engagement].

Criaremos um aplicativo básico com o Visual Studio para demonstrar a integração.

###Criação de um novo projeto do Windows Phone Silverlight

1. Inicie o Visual Studio e, na tela inicial, selecione **Novo Projeto....**.

2. Na janela pop-up, selecione `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`. Preencha o aplicativo `Name` e `Solution name` e, em seguida, clique em **OK**.

   	![][13]

3. Você pode escolher como destino a versão **Windows Phone 8.0** ou **Windows Phone 8.1**.

Você criou um novo aplicativo do Windows Phone Silverlight no qual integraremos o SDK do Mobile Engagement do Azure.

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Instalação do pacote nuget do [SDK do Windows Phone do Mobile Engagement] em seu projeto.

2. Abra `WMAppManifest.xml` (sob a pasta Propriedades) e verifique se o seguinte é declarado (adicione caso não estejam declarados) na marca `<Capabilities />`:

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. Agora cole a cadeia de conexão que você copiou anteriormente para o seu aplicativo do Mobile Engagement e cole-o no arquivo `Resources\EngagementConfiguration.xml` entre as marcas `<connectionString>` e `</connectionString>`:

	![][21]

4. No arquivo `App.xaml.cs`:

	a. Adicione a instrução `using`

			using Microsoft.Azure.Engagement;

	b. Inicializar o SDK no método `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Insira o seguinte em `Application_Activated`

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement. Vamos fazer isso ao subclassificar nosso `MainPage` com o `EngagementPage` que o SDK do Mobile Engagement fornece.

1. Adicione a instrução `using`:

		using Microsoft.Azure.Engagement;

2. Substitua a superclasse de `MainPage`, que fica antes de `PhoneApplicationPage`, por `EngagementPage`, conforme mostrado abaixo:

	![][22]

3. No arquivo `MainPage.xml`: a. Adicione às suas declarações de namespaces:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Substitua o `phone:PhoneApplicationPage` no nome da marca xml por `engagement:EngagementPage`

###Verifique se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo conecta o back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement.

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior.

	![][26]

2. Você será levado para a página de configurações do seu Portal do Engagement para seu aplicativo. A partir disso, clique na guia **Monitor** conforme mostrado abaixo. ![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo.

4. No Visual Studio, inicie o aplicativo no simulador ou em um dispositivo conectado.

5. Se ele funcionou, agora você deverá ver uma sessão no monitor! ![][33]

**Parabéns!** Você concluiu com êxito a primeira etapa deste tutorial com um aplicativo que se conecta ao back-end do Mobile Engagement, que já está enviando dados.

##<a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo

O Mobile Engagement permite interagir e ENTRAR EM CONTATO com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir irão configurar seu aplicativo para recebê-las.

###Habilitar o aplicativo para receber Notificações por Push do MPNS

Adicionar novos recursos ao arquivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###Inicializar o SDK do REACH

1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função `Application_Launching` logo após a inicialização do agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Em `App.xaml.cs`, chame `EngagementReach.Instance.OnActivated(e);` na função `Application_Activated` logo após a inicialização do agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Você está pronto, agora, verificaremos se você fez corretamente esta integração básica.

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora, criaremos uma campanha simples de Notificação por Push que enviará uma notificação por push para nosso aplicativo.

1. Navegue até a guia **REACH** em seu portal do Mobile Engagement

2. Clique em **Novo comunicado** para criar sua campanha de push. ![][35]

3. Configure o primeiro campo da campanha por meio das seguintes etapas: ![][36]

	1. Nomeie sua campanha com qualquer nome que desejar.
	2. Selecione a **Hora de entrega** como *Sempre* para permitir que o aplicativo receba uma notificação quer o aplicativo tenha sido iniciado ou não.
	3. No texto de notificação, digite o Título que estará em negrito no envio por push.
	4. Depois digite sua mensagem.

4. Role para baixo e, na seção de conteúdo, selecione **Notificação apenas**. ![][37]

5. Você terminou a configuração da campanha mais básica possível, agora role para baixo novamente e clique em **Criar** para salvar a sua campanha.

6. Última etapa, clique em **Ativar** para ativar sua campanha e enviar notificações por push. ![][39]

7. Você verá uma notificação no seu dispositivo, **Parabéns!**: ![][40]

<!-- URLs. -->
[SDK do Windows Phone do Mobile Engagement]: http://go.microsoft.com/?linkid=9874664
[SDK do Windows Phone para o Mobile Engagement]: http://go.microsoft.com/?linkid=9874664
[SDK do Windows Phone para o Mobile Engagement]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=July15_HO5-->