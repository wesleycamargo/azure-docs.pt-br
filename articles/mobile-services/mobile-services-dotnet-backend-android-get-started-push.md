<properties 
	pageTitle="Introdução ao envio por push (Android) | Microsoft Azure" 
	description="Saiba como usar os serviços móveis do Azure para enviar notificações por push para seu aplicativo .Net do Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="ricksal"/>

# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo Android. Neste tutorial você adiciona notificações por push usando o serviço de Mensagens de Nuvem do Google (GCM) para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Como tal, esse tutorial também requer o Visual Studio 2013.

>[AZURE.NOTE]Para obter a versão do Eclipse deste tutorial, consulte [Introdução às notificações por push (Eclipse)].
 
##<a id="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configurar o serviço móvel para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Atualizar o serviço para enviar notificações por push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a name="update-app"></a>Adicionar notificações de push para seu aplicativo

###Verificar Versão de SDK do Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo.

Se você for testar com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

###Inclua o Google Play Services no projeto

[AZURE.INCLUDE [Adicionar Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Incluir código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

##<a name="test-app"></a>Testar o aplicativo no serviço móvel publicado

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

###<a id="local-testing"></a>Habilitar notificações por push para teste local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Este tutorial foi concluído com êxito.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para usar Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que mostra como usar marcas para enviar notificações por push de um Serviço Móvel para somente um usuário autenticado.

+ [Enviar notificações em push para usuários autenticados]  
<br/>Saiba como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

+ [Enviar notificações de transmissão para assinantes]  
<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações com base no modelo para assinantes]  
<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução à autenticação do seu aplicativo][Get started with authentication] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Como usar a biblioteca de cliente Android para os Serviços Móveis] <br/>Saiba mais sobre como usar os Serviços Móveis com o Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Introdução às notificações por push (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-EC.md
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-android-get-started.md
[Introdução aos dados]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Como usar a biblioteca de cliente Android para os Serviços Móveis]: mobile-services-android-how-to-use-client-library.md

[Enviar notificações em push para usuários autenticados]: mobile-services-dotnet-backend-android-push-notifications-app-users.md
[Enviar notificações por push para usuários autenticados]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[O que são Hubs de Notificação?]: ../notification-hubs-overview.md
[Enviar notificações de transmissão para assinantes]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Enviar notificações com base no modelo para assinantes]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure Management Portal]: https://manage.windowsazure.com/
 

<!------HONumber=August15_HO7-->