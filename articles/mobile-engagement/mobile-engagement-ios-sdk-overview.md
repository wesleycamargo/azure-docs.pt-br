---
title: "Visão geral do SDK para iOS do Azure Mobile Engagement | Microsoft Docs"
description: "Atualizações e procedimentos mais recentes para o SDK do iOS para Mobile Engagement do Azure"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: c8bb1161e874a3adda4a71ee889ca833db881e20
ms.openlocfilehash: cd70b0b5656bef08a8be1c1a67754b203cceb905


---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>SDK do iOS para o Mobile Engagement do Azure
Comece aqui obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo iOS. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de&15; minutos](mobile-engagement-ios-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Procedimentos de integração
1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement.md)
2. Para Notificações: [Como integrar o Reach (Notificações) em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
### <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Melhor entrega de log em segundo plano.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK consulte os [Procedimentos de atualização](mobile-engagement-ios-upgrade-procedure.md)completos.

Para cada nova versão do SDK, você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

### <a name="from-300-to-400"></a>De 3.0.0 a 4.0.0
### <a name="xcode-8"></a>XCode 8
O XCode 8 é obrigatório desde a versão 4.0.0 do SDK.

> [!NOTE]
> Se você realmente depende do XCode 7, pode usar o [SDK do iOS Engagement v3.2.4](https://aka.ms/r6oouh). Há um bug conhecido no módulo de alcance nesta versão anterior durante a execução em dispositivos como iOS 10: as notificações de sistema não são acionadas. Para corrigir isso, você terá que implementar a API desaprovada `application:didReceiveRemoteNotification:` no representante do aplicativo da seguinte maneira:
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Não recomendamos essa solução alternativa** pois esse comportamento pode alterar qualquer atualização da versão futura iOS (até mesmo pequenas) porque esta API do iOS foi preterida. Você deve mudar para o XCode 8 assim que possível.
> 
> 

#### <a name="usernotifications-framework"></a>Estrutura UserNotifications
Você precisa adicionar a estrutura `UserNotifications` em suas fases de build.

no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Compilar fases"** e, no menu **"Link binário com bibliotecas"**, adicione a estrutura `UserNotifications.framework` – defina o link como `Optional`

#### <a name="application-push-capability"></a>Capacidade de envio por push do aplicativo
O XCode 8 pode redefinir a capacidade de envio por push do aplicativo. Verifique isso novamente na guia `capability` do destino selecionado.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Adicionar o novo código de registro de notificação do iOS 10
O trecho de código anterior para registrar o aplicativo para as notificações ainda funciona, mas está usando as APIs desaprovadas durante a execução no iOS 10. 

Importe a estrutura `User Notification` :

        #import <UserNotifications/UserNotifications.h>

No seu método representante do aplicativo `application:didFinishLaunchingWithOptions` , substitua:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

por:

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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Resolver conflitos de delegado UNUserNotificationCenter

*Se o aplicativo nem uma das bibliotecas de terceiros implementar um `UNUserNotificationCenterDelegate`, ignore esta parte.*

Um delegado `UNUserNotificationCenter` é usado pelo SDK para monitorar o ciclo de vida das notificações do Engagement em dispositivos que executam o iOS 10 ou superior. O SDK tem sua própria implementação do protocolo `UNUserNotificationCenterDelegate`, mas pode haver apenas um delegado `UNUserNotificationCenter` por aplicativo. Qualquer outro delegado adicionado ao objeto `UNUserNotificationCenter` entrará em conflito com o do Engagement. Se o SDK detectar seu delegado ou qualquer delegado de terceiros, ele não usará sua própria implementação, para lhe dar uma chance para resolver os conflitos. Você precisará adicionar a lógica do Engagement ao seu próprio delegado para resolver os conflitos.

Há duas maneiras de fazer isso.

Proposta 1: apenas encaminhando as chamadas do delegado para o SDK:

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

Ou proposta 2: herdando da classe `AEUserNotificationHandler`

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

> [!NOTE]
> Você pode determinar se uma notificação vem do Engagement ou não passando seu dicionário `userInfo` para o método da classe `isEngagementPushPayload:` do Agent.

Verifique se o delegado do objeto `UNUserNotificationCenter` é definido como seu delegado no método `application:willFinishLaunchingWithOptions:` ou `application:didFinishLaunchingWithOptions:` do delegado do aplicativo.
Por exemplo, se você implementou a proposta 1 acima:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }




<!--HONumber=Dec16_HO2-->


