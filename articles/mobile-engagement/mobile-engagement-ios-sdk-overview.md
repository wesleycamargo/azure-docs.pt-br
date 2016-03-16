<properties
	pageTitle="Visão geral do SDK do iOS no Mobile Engagement do Azure"
	description="Atualizações e procedimentos mais recentes para o SDK do iOS para Mobile Engagement do Azure"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#SDK do iOS para o Mobile Engagement do Azure

Comece aqui obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo iOS. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de 15 minutos](mobile-engagement-ios-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-ios-sdk-content.md)

##Procedimentos de integração
1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para Notificações: [Como integrar o Reach (Notificações) em seu aplicativo iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo iOS](mobile-engagement-ios-use-engagement-api.md)


##Notas de versão

###3\.2.1 (11/12/2015)

-   Corrigido o atraso quando uma nova instância do aplicativo é disparada por uma notificação com links profundos 

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-ios-release-notes.md)

##Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK consulte os [Procedimentos de atualização](mobile-engagement-ios-upgrade-procedure.md) completos.

Para cada nova versão do SDK, você deve primeiro substituir (remover e importar novamente no xcode) as pastas EngagementSDK e EngagementReach.

###De 2.0.0 a 3.0.0
Suporte removido para iOS 4.X. A partir de esta versão, o destino da implantação do seu aplicativo deve ter pelo menos o iOS 6.

Se você estiver usando o Reach em seu aplicativo, deverá adicionar o valor `remote-notification` à matriz `UIBackgroundModes` no arquivo Info.plist para receber notificações remotas.

O método `application:didReceiveRemoteNotification:` precisa ser substituído pelo `application:didReceiveRemoteNotification:fetchCompletionHandler:` em seu representante de aplicativo.

"AEPushDelegate.h" é uma interface preterida e você precisa remover todas as referências. Isso inclui remover o `[[EngagementAgent shared] setPushDelegate:self]` e os métodos representantes do seu representante de aplicativo:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_0302_2016-->