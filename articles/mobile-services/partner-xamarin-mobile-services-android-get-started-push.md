<properties
	pageTitle="Adicionar notificações por push ao aplicativo Xamarin Android | Microsoft Azure"
	description="Saiba como configurar notificações por push com o Google Cloud Messaging para aplicativos Xamarin.Android usando os Serviços Móveis do Azure e os Hubs de Notificação do Azure."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/17/2016"
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

Você já deve ter os componentes [Xamarin.Android] e [Serviços móveis do Azure] instalados em seu projeto de quando concluiu a [Introdução aos serviços móveis].

##<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configurar o serviço móvel para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Atualizar o script de inserção registrado para enviar notificações

>[AZURE.TIP] As etapas a seguir mostram como atualizar o script registrado para a operação de inserção na tabela TodoItem no Portal clássico do Azure. Você também pode acessar e editar este script de serviço móvel diretamente no Visual Studio, no nó do Azure do Gerenciador de Servidores.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>Configurar o projeto existente para notificações por push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>Testar notificações por push no seu aplicativo

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Este tutorial foi concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução à autenticação](mobile-services-android-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?](../notification-hubs/notification-hubs-overview.md) <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Como usar a biblioteca de cliente .NET de Serviços Móveis](mobile-services-dotnet-how-to-use-client-library.md) <br/>Saiba mais sobre como usar os Serviços Móveis com código Xamarin C#.

* [Referência de script de servidor dos Serviços Móveis](mobile-services-how-to-use-server-scripts.md) <br/>Saiba mais sobre como implementar a lógica de negócios ao seu serviço móvel.

<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-ios-get-started.md

[Componente do cliente Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Serviços móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0323_2016-->