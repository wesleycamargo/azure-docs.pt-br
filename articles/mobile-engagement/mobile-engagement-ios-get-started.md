<properties
	pageTitle="Introdução ao Engajamento Móvel do Azure para iOS em Objective C | Microsoft Azure"
	description="Aprenda a usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos iOS."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

# Introdução ao Mobile Engagement do Azure para aplicativos iOS em Objective C

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS. Neste tutorial, você cria um aplicativo iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS).

Este tutorial exige o seguinte:

+ XCode 8, que pode ser instalado a partir da MAC App Store
+ o [SDK do Mobile Engagement iOS]

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS.

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo iOS

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [integração do SDK do iOS do Mobile Engagement](mobile-engagement-ios-sdk-overview.md)

Criaremos um aplicativo básico com XCode para demonstrar a integração.

###Crie um novo projeto iOS

[AZURE.INCLUDE [Criar um novo projeto iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Conectar o aplicativo ao back-end do Mobile Engagement

1. Faça o download do [SKD do Mobile Engagement iOS].
2. Extraia o arquivo .tar.gz para uma pasta no seu computador.
3. Clique com o botão direito do mouse no projeto e selecione **Adicionar arquivos a**.

	![][1]

4. Navegue até a pasta na qual você extraiu o SDK e selecione a pasta `EngagementSDK`, depois pressione **OK**.

	![][2]

5. Abra a guia **Criar Fases** e, no menu **Vincular Binário com Bibliotecas**, adicione as estruturas como mostrado abaixo:

	![][3]

6. Volte para o portal do Azure na página **Informações de Conexão** de seu aplicativo e copie a cadeia de conexão.

	![][4]

7. Adicione a linha de código a seguir a seu arquivo **AppDelegate.m**.

		#import "EngagementAgent.h"

8. Agora, cole a cadeia de conexão no representante `didFinishLaunchingWithOptions`.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]   
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

9. `setTestLogEnabled` é uma instrução opcional que habilita os logs do SDK para que você possa identificar problemas.

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

1. Abra o arquivo **ViewController.h** e importe **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Agora, substitua a superclasse da interface **ViewController** por `EngagementViewController`:

	`@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite interagir com seus usuários e o REACH com notificações por push e mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

### Habilitar seu aplicativo para receber Notificações por push silenciosas

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Adicionar a biblioteca Reach ao seu projeto

1. Clique com o botão direito em seu projeto.
2. Selecione **Adicionar arquivo a**.
3. Navegue até a pasta onde você extraiu o SDK.
4. Selecione a pasta `EngagementReach`.
5. Clique em **Adicionar**.

### Modifique seu Representante do Aplicativo

1. De volta ao arquivo **AppDeletegate.m**, importe o módulo Engagement Reach.

		#import "AEReachModule.h"
		#import <UserNotifications/UserNotifications.h>

2. No método `application:didFinishLaunchingWithOptions`, crie um módulo Reach e passe-o para sua linha de inicialização do Engagement existente:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Habilite seu aplicativo para receber Notificações por Push do APNS

1. Adicione a seguinte linha ao método `application:didFinishLaunchingWithOptions`:

		if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
		{
			if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
			{
				[UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
			}else
			{
				[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
			}
			[application registerForRemoteNotifications];
		}
		else
		{
			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Adicione o método `application:didRegisterForRemoteNotificationsWithDeviceToken` da seguinte maneira:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
			NSLog(@"Registered Token: %@", deviceToken);
		}

3. Adicione o método `didFailToRegisterForRemoteNotificationsWithError` da seguinte maneira:

		- (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
		{
		   
		   NSLog(@"Failed to get token, error: %@", error);
		}

4. Adicione o método `didReceiveRemoteNotification:fetchCompletionHandler` da seguinte maneira:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK do Mobile Engagement iOS]: http://aka.ms/qk2rnj
[SKD do Mobile Engagement iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

<!---HONumber=AcomDC_0928_2016-->