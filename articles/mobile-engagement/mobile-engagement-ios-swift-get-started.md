<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para iOS em Swift"
	description="Aprenda a usar o Mobile Engagement do Azure com Análises e Notificações por Push para Aplicativos iOS."
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para Aplicativos iOS em Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS. Neste tutorial, você cria um aplicativo iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS).

Este tutorial exige o seguinte:

+ XCode 6 ou XCode 7, que podem ser instalados a partir da MAC App Store
+ o [SDK do Mobile Engagement iOS]
+ Certificado de notificação por push (. p12), que pode ser obtido no centro de desenvolvimento da Apple

> [AZURE.NOTE] Este tutorial usa o Swift versão 2.0.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS.

> [AZURE.IMPORTANT] A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS e para concluí-lo, você deve ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

##<a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo iOS

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [Integração do SDK do iOS no Mobile Engagement](../mobile-engagement-ios-sdk-overview/)

Criaremos um aplicativo básico com XCode para demonstrar a integração:

###Crie um novo projeto iOS

[AZURE.INCLUDE [Crie um novo projeto do iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Faça o download do [SKD do Mobile Engagement iOS]
2. Extraia arquivo o arquivo .tar.gz para uma pasta no seu computador
3. Clique com botão direito no projeto e selecione "Adicionar arquivos a..."

	![][1]

4. Navegue até a pasta onde você extraiu o SDK e selecione a `EngagementSDK` pasta, então, pressione OK.

	![][2]

5. Abra a guia `Build Phases` e no menu `Link Binary With Libraries`, adicione as estruturas, como mostrado abaixo: **OBSERVAÇÃO** Você deve incluir `CoreLocation, CFNetwork, CoreTelephony, and SystemConfiguration` :

	![][3]

6. Para o **XCode 7** - adicione `libxml2.tbd` em vez de `libxml2.dylib`.

7. Crie um cabeçalho Bridging para poder usar as APIs do Objective C do SDK escolhendo Arquivo > Novo > Arquivo > iOS > Fonte > Arquivo de Cabeçalho.

	![][4]

8. Edite o arquivo de cabeçalho ponte para expor o código em Objective-C do Mobile Engagement para o código Swift e adicione as seguintes importações:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. Em Configurações de Compilação, verifique se a definição da compilação do Bridging Header do Objective C em Compilador Swift - Geração de Código tem um caminho para esse cabeçalho. Aqui está um exemplo de caminho: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (dependendo do caminho)**

	![][6]

10. Volte para o portal do Azure na página *Informações de Conexão* de seu aplicativo e copie a Cadeia de Conexão

	![][5]

11. Agora, cole a cadeia de conexão em `didFinishLaunchingWithOptions` delegar

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Abra o arquivo **ViewController.swift** e substitua a classe base de **ViewController** por **EngagementViewController**:

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo

O Mobile Engagement permite interagir e ENTRAR EM CONTATO com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir irão configurar seu aplicativo para recebê-las.

### Habilitar seu aplicativo para receber Notificações por push silenciosas

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Adicionar a biblioteca Reach ao seu projeto

1. Clique com botão direito no projeto
2. Selecione `Add file to ...`
3. Navegue até a pasta onde você extraiu o SDK
4. Selecione a pasta `EngagementReach`
5. Clique em Adicionar
6. Edite o arquivo de cabeçalho ponte para expor os cabeçalhos de Acesso em Objective-C do Mobile Engagement e adicione as seguintes importações:

		/* Mobile Engagement Reach */
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
		#import "AEReachPollQuestion.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Modifique seu Representante do Aplicativo

1. Dentro de `didFinishLaunchingWithOptions`, crie um módulo do Reach e transmita-o para a linha de inicialização do Engagement existente:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Habilite seu aplicativo para receber Notificações por Push do APNS
1. Adicione a seguinte linha ao método `didFinishLaunchingWithOptions`:

		/* Ask user to receive push notifications */
		if #available(iOS 8.0, *)
		{
		   let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
		   application.registerUserNotificationSettings(settings)
		   application.registerForRemoteNotifications()
		}
		else
		{
		   application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
		}

2. Adicione o método `didRegisterForRemoteNotificationsWithDeviceToken` da seguinte maneira:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Adicione o método `didReceiveRemoteNotification:fetchCompletionHandler:` da seguinte maneira:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK do Mobile Engagement iOS]: http://aka.ms/qk2rnj
[SKD do Mobile Engagement iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0128_2016-->