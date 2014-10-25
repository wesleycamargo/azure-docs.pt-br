<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Como habilitar o sistema de Mensagens em Nuvem do Google

Este tópico mostra como habilitar seu aplicativo Android para notificações por push usando o sistema de Mensagens em Nuvem do Google (GCM). A chave de API obtida é usada para registrar o aplicativo Android para notificações por push no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Para ver o tutorial de passo a passo completo que inclui atualizações para seu aplicativo, consulte [Introdução às notificações por push][Introdução às notificações por push].

[WACOM.INCLUDE [Habilitar GCM][Habilitar GCM]]

Agora, você pode usar este valor de chave de API para habilitar serviços para autenticar com GCM e enviar notificações por push em nome de seu aplicativo.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Habilitar GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
