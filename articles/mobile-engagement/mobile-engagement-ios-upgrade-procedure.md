<properties
	pageTitle="Procedimento de atualização do SDK para iOS do Engajamento Móvel do Azure | Microsoft Azure"
	description="Atualizações e procedimentos mais recentes para o SDK do iOS para Mobile Engagement do Azure"
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
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Para cada nova versão do SDK, você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

##De 3.0.0 a 4.0.0

### XCode 8
O XCode 8 é obrigatório desde a versão 4.0.0 do SDK.

> [AZURE.NOTE] Se você realmente depende do XCode 7, pode usar o [SDK do iOS Engagement v3.2.4](https://aka.ms/r6oouh). Há um bug conhecido no módulo de alcance nesta versão anterior durante a execução em dispositivos como iOS 10: as notificações de sistema não são acionadas. Para corrigir isso, você terá que implementar a API preterida `application:didReceiveRemoteNotification:` em seu aplicativo delegado da seguinte maneira:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Não recomendamos essa solução alternativa** pois esse comportamento pode alterar qualquer atualização da versão futura iOS (até mesmo pequenas) porque esta API do iOS foi preterida. Você deve mudar para o XCode 8 assim que possível.

### Estrutura UserNotifications
Você precisa adicionar a estrutura `UserNotifications` em suas fases de build.

no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Compilar fases"** e no menu **"Link binário com bibliotecas"**, adicione a estruturas `UserNotifications.framework` – defina o link como `Optional`

### Capacidade de envio por push do aplicativo
O XCode 8 pode redefinir a capacidade de envio por push do aplicativo. Verifique isso novamente na guia `capability` do destino selecionado.

### Se você já tiver sua própria implementação de UNUserNotificationCenterDelegate

O SDK também tem sua própria implementação do protocolo UNUserNotificationCenterDelegate. Ele é usado pelo SDK para monitorar o ciclo de vida de notificações do Engagement em dispositivos que executam o iOS 10 ou superior. Se o SDK detectar seu representante, ele não usará sua própria implementação porque poderá haver apenas um delegado UNUserNotificationCenter por aplicativo. Isso significa que você precisa adicionar a lógica do Engagement a seu próprio delegado.

Há duas maneiras de fazer isso.

Encaminhando suas chamadas delegadas ao SDK:

	#import <UIKit/UIKit.h>
	#import "EngagementAgent.h"
	#import <UserNotifications/UserNotifications.h>


	@interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
	@end

	@implementation MyAppDelegate

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
	}
	@end

Ou herdando da classe `AEUserNotificationHandler`

	#import "AEUserNotificationHandler.h"
	#import "EngagementAgent.h"

	@interface CustomUserNotificationHandler :AEUserNotificationHandler
	@end

	@implementation CustomUserNotificationHandler

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}

	@end

> [AZURE.NOTE] Você pode determinar se uma notificação vem do Engagement ou não, passando seu dicionário `userInfo` para o método da classe `isEngagementPushPayload:` do Agent.

##De 2.0.0 a 3.0.0
Suporte removido para iOS 4.X. A partir de esta versão, o destino da implantação do seu aplicativo deve ter pelo menos o iOS 6.

Se você estiver usando o Reach em seu aplicativo, deverá adicionar o valor `remote-notification` à matriz `UIBackgroundModes` no arquivo Info.plist para receber notificações remotas.

O método `application:didReceiveRemoteNotification:` precisa ser substituído pelo `application:didReceiveRemoteNotification:fetchCompletionHandler:` em seu representante de aplicativo.

"AEPushDelegate.h" é uma interface preterida e você precisa remover todas as referências. Isso inclui remover o `[[EngagementAgent shared] setPushDelegate:self]` e os métodos representantes do seu representante de aplicativo:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##De 1.16.0 a 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Mobile Engagement do Azure. Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.16 e depois aplicar o procedimento a seguir

>[AZURE.IMPORTANT] O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement

### Agente

O método `registerApp:` foi substituído pelo novo método `init:`. Seu representante de aplicativo deve ser atualizado de acordo e usar a cadeia de conexão:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

O rastreamento de SmartAd foi removido do SDK, você só precisa remover todas as instâncias da classe `AETrackModule`

### Alterações de nome de classe

Como parte da renovação de marca, há alguns nomes de classe/arquivos que precisam ser alterados.

Todas as classes prefixadas com “CP” foram renomeadas com o prefixo “AE”.

Exemplo:

-   `CPModule.h` foi renomeado para `AEModule.h`.

Todas as classes prefixadas com “Capptain” foram renomeadas com o prefixo “Engagement”.

Exemplos:

-   A classe `CapptainAgent` foi renomeada para `EngagementAgent`.
-   A classe `CapptainTableViewController` foi renomeada para `EngagementTableViewController`.
-   A classe `CapptainUtils` foi renomeada para `EngagementUtils`.
-   A classe `CapptainViewController` foi renomeada para `EngagementViewController`.

<!---HONumber=AcomDC_0921_2016-->