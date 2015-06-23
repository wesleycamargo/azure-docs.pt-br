<properties 
	pageTitle="Adicionar notificações por push ao seu aplicativo de Serviços Móveis - Serviços Móveis" 
	description="Saiba como usar notificações por push em aplicativos Android Xamarin com serviços móveis do Azure." 
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
	ms.date="03/12/2015" 
	ms.author="glenga"/>

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Xamarin.Android. Neste tutorial você adiciona notificações por push usando o serviço Google Cloud Messaging (GCM) para o projeto [Introdução aos Serviços Móveis]. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Habilitar o Google Cloud Messaging](#register)
2. [Configurar os Serviços Móveis](#configure)
4. [Configurar o projeto para notificações por push](#configure-app)
5. [Adicionar código de notificações por push ao seu aplicativo](#add-push)
6. [Inserir dados para receber notificações](#test)

Este tutorial exige o seguinte:

+ Uma conta ativa do Google.
+ [Componente de cliente de mensagens em nuvem do Google]. Você adicionará esse componente durante o tutorial.

Você já deve ter os componentes [Xamarin.Android] e os [Serviços Móveis do Azure][Azure Mobile Services Component] instalados em seu projeto desde quando concluiu a [Introdução aos Serviços Móveis] ou [Adicionar os Serviços Móveis a um aplicativo existente].

##<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configurar o serviço móvel para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Atualizar o script de inserção registrado para enviar notificações

>[AZURE.NOTE]As etapas a seguir mostram como atualizar o script registrado para a operação de inserção na tabela TodoItem no Portal de Gerenciamento do Azure. Você também pode acessar e editar este script de serviço móvel diretamente no Visual Studio, no nó do Azure do Gerenciador de Servidores.

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

* [Adicionar os Serviços Móveis a um aplicativo existente] <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação](mobile-services-android-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando os Serviços Móveis.

* [O que são Hubs de Notificação?](../notification-hubs-overview.md) <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Como usar a biblioteca de cliente .NET de Serviços Móveis](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Saiba mais sobre como usar os Serviços Móveis com código Xamarin C#.

* [Referência de script de servidor dos Serviços Móveis](mobile-services-how-to-use-server-scripts.md) <br/>Saiba mais sobre como implementar a lógica de negócios ao seu serviço móvel.

<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-ios-get-started.md
[Adicionar os Serviços Móveis a um aplicativo existente]: mobile-services-android-get-started-data.md

[Componente de cliente de mensagens em nuvem do Google]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
<!--HONumber=54--> 