<properties
	pageTitle="Introdução aos Serviços Móveis para aplicativos Xamarin Android | Microsoft Azure"
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo Android Xamarin"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="glenga"/>

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para obter a versão equivalente dos Aplicativos Móveis deste tópico, veja [Adicionar notificações por push ao aplicativo Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md).

##Visão geral

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Xamarin.Android. Neste tutorial, você adiciona notificações por push usando o serviço GCM (Google Cloud Messaging) para o projeto de [Introdução aos Serviços Móveis]. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial exige o seguinte:

+ Uma conta ativa do Google.
+ [Componente do cliente Google Cloud Messaging]. Você adicionará esse componente durante o tutorial.

Você já deve ter os componentes [Xamarin.Android] e os [Serviços Móveis do Azure][Azure Mobile Services Component] instalados em seu projeto de quando concluiu a [Introdução aos Serviços Móveis].

##<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [Habilitar o GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configurar o serviço móvel para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Atualizar o serviço para enviar notificações por push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Configurar o projeto existente para notificações por push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Testar o aplicativo no serviço móvel publicado

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

###<a id="local-testing"></a>Habilitar notificações por push para teste local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Componente do cliente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0323_2016-->