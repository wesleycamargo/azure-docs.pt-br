<properties
	pageTitle="Como usar vários clientes com um único back-end de serviço móvel | Serviços Móveis do Azure"
	description="Saiba como usar um back-end de serviço móvel único de vários aplicativos cliente destinados a diferentes plataformas móveis."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="glenga"/>

# Oferecendo suporte a várias plataformas de dispositivo de um único serviço móvel

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Um dos principais benefícios de usar os Serviços Móveis do Azure no desenvolvimento de aplicativos móveis é a capacidade de usar um único serviço de back-end que oferece suporte a um aplicativo em várias plataformas de cliente. Os Serviços Móveis fornecem bibliotecas de cliente nativas para todas as principais plataformas de dispositivos, o que facilita o desenvolvimento de aplicativos usando um único serviço de back-end e usando ferramentas de desenvolvedor entre plataformas. Este tópico discute considerações para que o seu aplicativo execute em várias plataformas clientes ao usar um único serviço móvel de back-end:

##<a id="push"></a>Notificações por push de plataformas cruzadas

Os Serviços Móveis usam os Hubs de Notificação do Azure para enviar notificações para aplicativos cliente em todas as principais plataformas de dispositivos. Os Hubs de Notificação fornecem uma infraestrutura consistente e unificada para criar e gerenciar registros de dispositivos e para enviar notificações por push entre plataformas. Os Hubs de Notificação oferecem suporte para notificações por push usando os serviços de notificação específicos da plataforma:

+ Serviço de Notificação por Push da Apple (APNS) para aplicativos do iOS
+ Serviço de Mensagens de Nuvem do Google (GCM) para aplicativos do Android
+ Serviço de Notificação do Windows (WNS) para a Windows Store, Windows Phone 8.1 Store e aplicativos universais do Windows
+ O Serviço de Notificação por Push da Microsoft (MPNS) para aplicativos do Windows Phone Silverlight

Para obter mais informações, consulte [Hubs de Notificação do Azure].

Os registros do cliente são criados usando a função registro na biblioteca do cliente dos Serviços Móveis específicos da plataforma ou usando as APIs REST dos Serviços Móveis. Os Hubs de Notificação oferecem suporte para dois tipos de registros de dispositivo:

+ **Registro nativo**<br/>Os registros nativos são adaptados para o serviço de notificação por push específico da plataforma. Ao enviar notificações para dispositivos registrados usando os registros nativos, você deve chamar as APIs específicas da plataforma no seu serviço móvel. Para enviar uma notificação para dispositivos em várias plataformas é necessário várias chamadas específicas da plataforma.

+ **Registro de modelos**<br/>Os Hubs de Notificação também oferecem suporte aos registros de modelos específicos da plataforma. Ao usar os registros de modelos específicos da plataforma, você pode usar uma única chamada de API para enviar uma notificação a seu aplicativo em execução em qualquer plataforma registrada. Para obter mais informações, consulte [Enviar notificações entre plataformas aos usuários].

As tabelas nas seções a seguir estão associadas aos tutoriais específicos do cliente que mostram a você como implementar as notificações por push dos serviços móveis de back-end do .NET e JavaScript.

###Back-end do .NET

Em um serviço móvel de back-end do .NET, você envia notificações ao chamar o método [SendAsync] no objeto [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) obtido da propriedade [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). A notificação por push enviada (nativa ou modelo) depende do objeto derivado de [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) específico que é passado ao método [SendAsync], como mostrado na seguinte tabela:

|Plataforma |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|Native|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

O código a seguir envia uma notificação por push de um serviço de back-end do .NET para todos os registros de dispositivo do iOS e Windows Store:

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Para exemplos de como enviar notificações por push para outras plataformas de cliente nativas, clique nos links da plataforma no cabeçalho da tabela acima.

Quando você usa registros de cliente modelo em vez de registros de cliente nativo, você pode enviar a mesma notificação com apenas uma única chamada para [SendAsync], fornecendo um objeto [TemplatePushMessage], como a seguir:

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###Back-end do JavaScript

Em um serviço móvel de back-end do JavaScript, você envia notificações ao chamar o método **send** no objeto específico da plataforma do [objeto de push] global, conforme exibido na tabela:

|Plataforma |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Native|[objeto apns](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[objeto gcm](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[objeto wns](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [objeto mpns](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

O código a seguir envia notificação por push para todos os registros do Android e Windows Phone:

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Para exemplos de como enviar notificações por push para outras plataformas de cliente nativas, clique nos links da plataforma no cabeçalho da tabela acima.

Quando você usa registros de cliente modelo em vez de registros de cliente nativo, você pode enviar a mesma notificação com apenas uma única chamada para a função **send** no [objeto de push] global, fornecendo uma carga de mensagem de modelo, como a seguir:

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>Desenvolvimento de aplicativos de plataformas cruzadas
Desenvolver aplicativos de dispositivo móvel nativo para todas as mais importantes plataformas de dispositivo exige que você (ou sua organização) tenham experiência em pelo menos nas linguagens de programação Objective-C, Java e C# ou JavaScript. Devido à despesa de desenvolver entre essas plataformas diferentes, alguns desenvolvedores escolheram uma experiência com base no navegador da Web para seus aplicativos. No entanto, tais experiências com base na Web não podem acessar a maioria dos recursos nativos que fornecem a rica experiência que os usuário passaram a esperar nos seus dispositivos móveis.

As ferramentas entre plataformas que fornecem uma experiência nativa mais rica em um dispositivo móvel, enquanto ainda compartilham uma única base de código, geralmente o JavaScript, estão disponíveis. Os Serviços Móveis facilitam criar e gerenciar um serviço de back-end para plataformas desenvolvimento de aplicativos entre plataformas fornecendo tutoriais de início rápido para as seguintes plataformas de desenvolvimento:

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>O Appcelerator permite que você use o JavaScript para desenvolver um único aplicativo que foi compilado para executar como nativo em todas as plataformas de dispositivo móveis. Isto nos fornece uma experiência de usuário rica na interface de usuário, acesso a todos os recursos de dispositivo nativos e desempenho de aplicativos nativos. Para obter mais informações, consulte o [tutorial Appcelerator][Appcelerator].

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (uma distribuição do projeto Apache Cordova) é uma estrutura de código-fonte aberto que permite que você use APIs da Web padronizadas, HTML e JavaScript para desenvolver um único aplicativo que executa nos dispositivos do Android, iOS e Windows. O PhoneGap fornece uma interface de usuário com base na exibição da Web, mas com uma experiência de usuário aprimorada ao acessar recursos nativos no dispositivo, como as notificações por push, o acelerômetro, câmera, armazenamento, geolocalização e navegador no aplicativo. Para obter mais informações, consulte o [tutorial de introdução rápida ao PhoneGap][PhoneGap].

	O Visual Studio agora também permite que você desenvolva aplicativos Cordova entre plataformas usando as extensões de aplicativos híbridos de múltiplos dispositivos para o Visual Studio, que é um software de pré-lançamento. Para obter mais informações, consulte [Introdução aos aplicativos híbridos de múltiplos dispositivos usando o HTML e JavaScript](http://msdn.microsoft.com/library/dn771545.aspx).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>O Sencha Touch fornece um conjunto de controles otimizados para telas sensíveis ao toque, que fornece uma experiência como a nativa em uma grande variedade de dispositivos a partir de uma base de códigos JavaScript e HTML única. O Sencha Touch pode ser usado juntamente com as bibliotecas do PhoneGap ou Cordova para fornecer aos usuários acesso aos recursos de dispositivo nativos. Para obter mais informações, consulte o [tutorial de introdução rápida ao Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>O Xamarin permite que você crie aplicativos totalmente nativos para os dispositivos iOS e Android, com interface de usuário totalmente nativas e acesso a todos os recursos do dispositivo. Os aplicativos Xamarin são codificados em C# em vez do Objective-C e Java. Isto permite que os desenvolvedores do .NET publiquem aplicativos para o iOS e Android e compartilhem código a partir dos projetos do Windows. O Xamarin fornece uma experiência de usuário totalmente nativa nos dispositivos iOS e Android a partir do código C#. Isto permite que você reutilize alguns dos códigos dos Serviços Móveis dos aplicativos do Windows nos dispositivos iOS e Android. Para obter mais informações, consulte [Desenvolvimento do Xamarin](#xamarin) abaixo.


<!-- URLs -->
[Hubs de Notificação do Azure]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Enviar notificações entre plataformas aos usuários]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[objeto de push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=AcomDC_1203_2015-->