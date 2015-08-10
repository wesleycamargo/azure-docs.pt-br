<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para iOS em Swift"
	description="Aprenda a usar o Mobile Engagement do Azure com Análises e Notificações por Push para Aplicativos iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para Aplicativos iOS em Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS. Neste tutorial, você cria um aplicativo iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos do destino com base em suas propriedades dos dispositivos.

Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Siga o tutorial a seguir para aprender a usar o Mobile Engagement para endereçar usuários e grupos de dispositivos específicos.

Este tutorial exige o seguinte:

+ XCode, que pode ser instalado a partir da MAC App Store
+ o [SDK do Mobile Engagement iOS]
+ Certificado de notificação por push (. p12), que pode ser obtido no centro de desenvolvimento da Apple

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS.

> [AZURE.IMPORTANT]A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS e para concluí-lo, você deve ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configuração do Mobile Engagement para seu aplicativo

1. Faça logon no Portal de Gerenciamento do Azure e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, **Mobile Engagement**, então, em **Criar**.

   	![][7]

3. No pop-up exibido, insira as seguintes informações:

   	![][8]

	- **Nome do Aplicativo**: digite o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
	- **Plataforma**: selecione a plataforma de destino (\*\*iOS\*\*) para o aplicativo (se seu aplicativo for destinado para várias plataformas, repita esse tutorial para cada uma).
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

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [documentação do SDK do Mobile Engagement iOS]

Criaremos um aplicativo básico com XCode para demonstrar a integração:

###Crie um novo projeto iOS

Você pode ignorar esta etapa se já tiver um aplicativo e estiver familiarizado com o desenvolvimento do iOS

1. Inicie o Xcode e no popup, selecione **Criar um novo projeto do Xcode**

   	![][12]

2. Selecione **Aplicativo de Modo de Exibição Único** e clique em Próximo

   	![][14]

3. Preencha o **Nome do Produto**, **Nome da Organização** e **Identificador da Organização**. Verifique se você selecionou **Swift** no idioma.

   	![][40]

O Xcode criará o aplicativo de demonstração no qual integraremos o Mobile Engagement

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Faça o download do [SKD do Mobile Engagement iOS]
2. Extraia arquivo o arquivo .tar.gz para uma pasta no seu computador
3. Clique com botão direito no projeto e selecione "Adicionar arquivos a..."

	![][17]

4. Navegue até a pasta onde você extraiu o SDK e selecione a `EngagementSDK` pasta, então, pressione OK.

	![][18]

5. Abra a guia `Build Phases` e no menu `Link Binary With Libraries`, adicione as estruturas, como mostrado abaixo:

	![][19]

6. Crie um cabeçalho Bridging para poder usar as APIs do Objective C do SDK escolhendo Arquivo > Novo > Arquivo > iOS > Fonte > Arquivo de Cabeçalho.

	![][41]

7. Edite o arquivo bridging header para exibir o código AzME do Objective C para o código Swift e adicione as seguintes importações:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. Em Configurações de Compilação, verifique se a definição da compilação do Bridging Header do Objective C em Compilador Swift - Geração de Código tem um caminho para esse cabeçalho. Aqui está um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (dependendo do caminho)**

9. Volte para o portal do Azure na página *Informações de Conexão* de seu aplicativo e copie a Cadeia de Conexão

	![][11]

10. Agora, cole a cadeia de conexão em `didFinishLaunchingWithOptions` delegar

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

- Abra o arquivo `ViewController.h`, importe `EngagementViewController.h` e substitua a superclasse da interface `ViewController` por `EngagementViewController`.

###Verifique se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo conecta o back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão "Engajar" na parte inferior:

	![][26]

2. Você será levado para a página de configurações no Portal do Engagement de seu aplicativo. Nela, clique na guia "Monitorar":

	![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo:

	![][31]

4. No Xcode, inicie o aplicativo no simulador ou em um dispositivo conectado

5. Se ele funcionou, agora você deverá ver uma sessão no monitor!

**Parabéns!** Você teve êxito na primeira etapa deste tutorial e tem um aplicativo que conecta o back-end do Mobile Engagement, e que já está enviando dados

6. Clicar no botão Home do simulador retornará o número de sessões no monitor para 0, conforme mostrado acima

	![][33]

##<a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo

O Mobile Engagement permite interagir e ENTRAR EM CONTATO com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir irão configurar seu aplicativo para recebê-las.

### Adicionar a biblioteca Reach ao seu projeto

1. Clique com botão direito no projeto
2. Selecione `Add file to ...`
3. Navegue até a pasta onde você extraiu o SDK
4. Selecione a pasta `EngagementReach`
5. Clique em Adicionar
6. Edite o arquivo bridging header para exibir os cabeçalhos Reach do AzME do Objective C e adicione as seguintes importações:

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Modifique seu Representante do Aplicativo

1. Dentro de `didFinishLaunchingWithOptions`, crie um módulo reach e passe-o para sua linha de inicialização do Engagement existente:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Habilite seu aplicativo para receber Notificações por Push do APNS
1. Adicione a seguinte linha ao método `didFinishLaunchingWithOptions`:

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. Adicione o método `didRegisterForRemoteNotificationsWithDeviceToken` da seguinte maneira:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Adicione o método `didReceiveRemoteNotification` da seguinte maneira:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###Conceda acesso a seu Certificado Push para o Mobile Engagement

Para permitir que o Mobile Engagement envie Notificações por Push em seu nome, você precisa conceder acesso ao certificado. Isso é feito configurando e inserindo seu certificado no portal do Mobile Engagement. Verifique se você obtém seu certificado. p12 conforme explicado na documentação da Apple.

1. Navegue até o portal do Mobile Engagement. Verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão "Engajar" na parte inferior:

	![][26]

2. Você será levado para a página de configurações no Portal do Engagement. Nela, clique na seção "Push Nativo" para carregar seu certificado p12:

	![][27]

3. Selecione seu p12, carregue-o e digite sua senha:

	![][28]

4. Agora, adicione o perfil de provisionamento e crie seu aplicativo para um dispositivo de destino

Já está pronto. Agora, verificaremos se você fez corretamente a integração básica

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora, criaremos uma campanha de Notificação por Push simples que enviará um push para nosso aplicativo:

1. Navegue até a guia Reach em seu portal do Mobile Engagement

2. clique em **Novo Comunicado** para criar sua campanha de por push

	![][35]

3. Configure o primeiro campo de sua campanha:

	![][36]

	- 	Nomeie sua campanha com qualquer nome desejado
	- 	Selecione a Hora de entrega como "Fora do aplicativo somente": esse é o tipo de notificação por push da Apple simples que apresenta algum texto.
	- 	No texto da notificação, primeiro digite o Título que será a primeira linha no push
	- 	Em seguida, digite a mensagem que será a segunda linha


4. Role para baixo e, na seção de conteúdo, selecione "Notificação apenas"

	![][37]

5. Você terminou a configuração da campanha mais básica possível, agora role novamente e crie sua campanha para salvá-la! ![][38]

6. Como uma última etapa, ative sua campanha ![][39]

7. Você verá uma notificação por push em seu dispositivo!

<!-- URLs. -->
[SDK do Mobile Engagement iOS]: http://go.microsoft.com/?linkid=9864553
[SKD do Mobile Engagement iOS]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-swift-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-swift-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-swift-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-swift-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png
 

<!---HONumber=July15_HO5-->