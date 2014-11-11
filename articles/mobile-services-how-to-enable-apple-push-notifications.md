<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Como habilitar notificações por push da Apple

Este tópico mostra como habilitar seu aplicativo iOS para notificações por push usando o Serviço de Notificação por Push da Apple (APNS). O certificado obtido é usado para registrar o aplicativo iOS para notificações por push no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Para ver o tutorial de passo a passo completo que inclui atualizações para seu aplicativo, consulte [Introdução às notificações por push][Introdução às notificações por push].

[WACOM.INCLUDE [Habilitar Notificações por Push para Apple](../includes/enable-apple-push-notifications.md)]

Agora, você pode usar esse certificado .p12 para habilitar serviços para autenticar com APNS e enviar notificações por push em nome de seu aplicativo.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Habilitar Notificações por Push para Apple]: ../includes/enable-apple-push-notifications.md
