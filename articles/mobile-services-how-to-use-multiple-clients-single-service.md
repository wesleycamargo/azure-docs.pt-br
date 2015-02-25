<properties 
	pageTitle="Como usar vários clientes com um único back-end de serviços móveis | Serviços Móveis do Azure" 
	description="Saiba como usar um back-end de serviço móvel único de vários aplicativos cliente para diferentes plataformas móveis, incluindo Windows Store e Windows Phone." 
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
	ms.date="08/15/2014" 
	ms.author="glenga"/>

# Oferecendo suporte a várias plataformas de dispositivo de um único serviço móvel
 
Um dos principais benefícios de usar os Serviços Móveis do Azure no desenvolvimento de aplicativos móveis é a capacidade de usar um único serviço de back-end que dá suporte a um aplicativo em várias plataformas de cliente. Os Serviços Móveis fornecem bibliotecas de cliente nativas para todas as principais plataformas de dispositivos, o que facilita o desenvolvimento de aplicativos usando um único serviço de back-end e usando ferramentas de desenvolvedor entre plataformas. Este tópico discute as seguintes considerações para que o seu aplicativo execute em várias plataformas clientes ao usar um único serviço móvel de back-end:

+ [Notificações por push de plataformas cruzadas](#push)
+ [Desenvolvimento de aplicativos de plataforma cruzada](#xplat-app-dev)
+ [Compartilhando código nos projetos do Visual Studio](#shared-vs)

Para obter mais informações gerais sobre os Serviços Móveis, consulte a [central de desenvolvedores de Serviços Móveis](/en-us/documentation/services/mobile-services/).

##<a id="push"></a>Notificações por push de plataformas cruzadas

Os Serviços Móveis usam os Hubs de Notificação do Azure para enviar notificações por push para aplicativos cliente em todas as principais plataformas de dispositivos. Os Hubs de Notificação fornecem uma infraestrutura consistente e unificada para criar e gerenciar registros de dispositivos e para enviar notificações por push entre plataformas. Os Hubs de Notificação dão suporte para notificações por push usando os serviços de notificação específicos da plataforma:

+ Serviço de Notificação por Push da Apple (APNS) para aplicativos do iOS
+ Serviço de Mensagens de Nuvem do Google (GCM) para aplicativos do Android
+ Serviço de Notificação do Windows (WNS) para a Windows Store, Windows Phone 8.1 Store e aplicativos universais do Windows 
+ O Serviço de Notificação por Push da Microsoft (MPNS) para aplicativos do Windows Phone Silverlight

>[AZURE.NOTE]Hubs de Notificação não dão suporte atualmente ao uso do WNS para enviar notificações por push a aplicativos do Windows Phone Silverlight 8.1. Você deve usar o MPNS para enviar notificações para o Silverlight e aplicativos do Windows Phone 8.0 e 7.0.

Para obter mais informações, consulte [Hubs de Notificação do Azure].

Os registros do cliente são criados usando a função registro na biblioteca do cliente dos Serviços Móveis específicos da plataforma ou usando as APIs REST dos Serviços Móveis. Os Hubs de Notificação dão suporte para dois tipos de registros de dispositivo:

+ **Registro nativo**<br/>Registros nativos são adaptados para o serviço de notificação por push específico da plataforma. Ao enviar notificações para dispositivos registrados usando os registros nativos, você deve chamar as APIs específicas da plataforma no seu serviço móvel. Para enviar uma notificação para dispositivos em várias plataformas é necessário várias chamadas específicas da plataforma.   
  
+ **Registro de modelo**<br/>Os Hubs de Notificação também dão suporte aos registros de modelo específicos da plataforma. Ao usar os registros de modelo específicos da plataforma, você pode usar uma única chamada à API para enviar uma notificação a seu aplicativo em execução em qualquer plataforma registrada. Para obter mais informações, consulte [Enviar notificações de plataforma cruzada aos usuários].

>[AZURE.NOTE]Ocorreu um erro ao tentar enviar uma mensagem para uma plataforma de dispositivo nativo para o qual não existe registros de dispositivo. Este erro não ocorre ao enviar notificações de modelos.

As tabelas nas seções a seguir estão associadas aos tutoriais específicos do cliente que mostram a você como implementar as notificações por push dos serviços móveis de back-end do .NET e JavaScript.  

###Back-end do .NET

Em um serviço móvel de back-end .NET, você envia notificações chamando o [SendAsync] método no [PushClient](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) objeto obtido da propriedade [ApiServices.Push](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). A notificação por push enviada (nativa ou modelo) depende do objeto específico derivado de [IPushMessage](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx), que é passado ao método [SendAsync], como mostrado na seguinte tabela: 

|Plataforma |[APNS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push)|[GCM](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push) |[WNS](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) |[MPNS](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)|
|-----|-----|----|----|-----|
|Nativo|[ApplePushMessage](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx)   |[GooglePushMessage](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx)     |[WindowsPushMessage](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

O código a seguir envia uma notificação por push de um serviço de back-end do .NET para todos os registros de dispositivo do iOS e Windows Store: 

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));    

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";
    
	// Envie notificações por push para todos os dispositivos iOS e Windows Store registrados. 
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Para exemplos de como enviar notificações por push para outras plataformas de cliente nativas, clique nos links da plataforma no cabeçalho da tabela acima. 

Quando você usa registros de cliente do modelo em vez de registros de cliente nativo, pode enviar a mesma notificação com apenas uma única chamada para [SendAsync], fornecendo um objeto [TemplatePushMessage] como a seguir: 

	// Crie uma nova mensagem de modelo e adicione o parâmetro 'message'.    
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Envie uma notificação por push para todos os registros de modelo.
    await Services.Push.SendAsync(templatePayload); 
 
###Back-end do JavaScript

IEm um serviço móvel de back-end do JavaScript, você envia notificações ao chamar o método **send** no objeto específico da plataforma, obtida do [objeto de push] global, conforme mostrado na seguinte tabela: 

|Plataforma |[APNS](/en-us/documentation/articles/mobile-services-javascript-backend-ios-get-started-push)|[GCM](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push) |[WNS](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push) |[MPNS](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push)|
|-----|-----|----|----|-----|
|Nativa|[apns object](http://msdn.microsoft.com/en-us/library/azure/jj839711.aspx)   |[gcm object](http://msdn.microsoft.com/en-us/library/azure/dn126137.aspx)     |[wns object](http://msdn.microsoft.com/en-us/library/azure/jj860484.aspx) | [mpns object](http://msdn.microsoft.com/en-us/library/azure/jj871025.aspx) |

O código a seguir envia notificação por push para todos os registros do Android e Windows Phone: 

	// Defina uma notificação por push para o GCM.
	var gcmPayload = 
    '{"data":{"message" : item.text }}';

	// Defina a carga de uma notificação do sistema do Windows Phone.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Envie notificações por push para todos os dispositivos Android e Windows Phone 8.0 registrados. 
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

Quando você usa registros de cliente do modelo em vez de registros de cliente nativo, pode enviar a mesma notificação com apenas uma única chamada para a função **send** no [objeto de push] global, fornecendo uma carga de mensagem de modelo, como a seguir: 

	// Crie uma nova mensagem de modelo com o parâmetro 'message'.    
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

##<a id="xplat-app-dev"></a>Desenvolvimento de aplicativos de plataforma cruzada
Desenvolver aplicativos de dispositivo móvel nativo para todas as mais importantes plataformas de dispositivo exige que você (ou sua organização) tenham experiência em pelo menos nas linguagens de programação Objective-C, Java e C# ou JavaScript. Devido à despesa de desenvolver entre essas plataformas diferentes, alguns desenvolvedores escolheram uma experiência com base no navegador da Web para seus aplicativos. No entanto, tais experiências com base na Web não podem acessar a maioria dos recursos nativos que fornecem a rica experiência que os usuário passaram a esperar nos seus dispositivos móveis.  

As ferramentas entre plataformas que fornecem uma experiência nativa mais rica em um dispositivo móvel, enquanto ainda compartilham uma única base de código, geralmente o JavaScript, estão disponíveis. Os Serviços Móveis facilitam criar e gerenciar um serviço de back-end para plataformas desenvolvimento de aplicativos entre plataformas fornecendo tutoriais de início rápido para as seguintes plataformas de desenvolvimento: 

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>O Appcelerator permite que você use o JavaScript para desenvolver um único aplicativo que foi compilado para executar como nativo em todas as plataformas de dispositivo móvel. Isto nos fornece uma experiência de usuário rica na interface de usuário, acesso a todos os recursos de dispositivo nativos e desempenho de aplicativos nativos. Para obter mais informações, consulte o [tutorial do Appcelerator][Appcelerator].
 
+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (uma distribuição do projeto Apache Cordova) é uma estrutura de software livre que permite que você use APIs da Web padronizadas, HTML e JavaScript para desenvolver um único aplicativo que executa nos dispositivos do Android, iOS e Windows. O PhoneGap fornece uma interface de usuário com base na exibição da Web, mas com uma experiência de usuário aprimorada ao acessar recursos nativos no dispositivo, como as notificações por push, o acelerômetro, câmera, armazenamento, geolocalização e navegador no aplicativo. Para obter mais informações, consulte o [tutorial de início rápido do PhoneGap][PhoneGap]. 
	
	O Visual Studio agora também permite que você desenvolva aplicativos Cordova entre plataformas usando as extensões de aplicativos híbridos de vários dispositivos para o Visual Studio, que é um software de pré-lançamento. Para obter mais informações, consulte [Introdução a aplicativos híbridos de vários dispositivos usando o HTML e o JavaScript](http://msdn.microsoft.com/en-us/library/dn771545.aspx). 

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>O Sencha Touch fornece um conjunto de controles otimizados para telas sensíveis ao toque, que oferecem uma experiência como a nativa em uma grande variedade de dispositivos por meio de uma base de códigos JavaScript e HTML única. O Sencha Touch pode ser usado juntamente com as bibliotecas do PhoneGap ou Cordova para fornecer aos usuários acesso aos recursos de dispositivo nativos. Para obter mais informações, consulte o [tutorial de início rápido do Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>O Xamarin permite que você crie aplicativos totalmente nativos para os dispositivos iOS e Android, com interface de usuário totalmente nativas e acesso a todos os recursos do dispositivo. Os aplicativos Xamarin são codificados em C# em vez do Objective-C e Java. Isto permite que os desenvolvedores do .NET publiquem aplicativos para o iOS e Android e compartilhem código por meio dos projetos do Windows. O Xamarin fornece uma experiência de usuário totalmente nativa nos dispositivos iOS e Android por meio do código C#. Isto permite que você reutilize alguns dos códigos dos Serviços Móveis dos aplicativos do Windows nos dispositivos iOS e Android. Para obter mais informações, consulte [Desenvolvimento do Xamarin](#xamarin) abaixo. 

	Você pode criar aplicativos Xamarin usando Xamarin Studio ou Visual Studio 2013. Para obter mais informações, consulte [Desenvolvimento de plataforma cruzada no Visual Studio](http://msdn.microsoft.com/en-us/library/dn771552.aspx).


##<a id="shared-vs"></a>Compartilhando e reutilizando código nos projetos do Visual Studio

Os Serviços Móveis incluem uma biblioteca cliente do .NET, que é uma Biblioteca de Classes Portátil do .NET Framework (PCL) que fornece suporte para o desenvolvimento em todas as plataformas do Windows. Para obter mais informações, consulte [Como usar um cliente .NET com os Serviços Móveis]. Isto facilita a reutilização do mesmo código dos Serviços Móveis, como para acesso de dados ou autenticação, em vários projetos C#.

Uma abordagem geral para compartilhar e reutilizar o seu código C# entre projetos é implementar o padrão MVVM (Model-View-ViewModel) e compartilhar assemblies em várias plataformas. É possível implementar o modelo e exibir as classes do modelo em um projeto da Biblioteca de Classes Portátil no Visual Studio e, em seguida, criar exibições personalizadas para diferentes plataformas. O código do modelo comum entre as plataformas pode, por exemplo, recuperar os dados de uma origem, como um serviço móvel de uma maneira independente de plataforma. A biblioteca MSDN fornece uma <a href="http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110)">visão geral e exemplo</a>, discussão sobre <a href="http://msdn.microsoft.com/en-us/library/gg597392(v=vs.110)">as diferenças de API</a>, um exemplo de como <a href="http://msdn.microsoft.com/en-us/library/hh563947(v=vs.110)">usar bibliotecas de classes portáteis para implementar o padrão MVVM</a>, orientação prescritiva <a href="http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj714086(v=vs.105).aspx">adicional</a>, e informações sobre <a href="http://msdn.microsoft.com/en-us/library/hh871422(v=vs.110)">Gerenciando recursos</a> em projetos de biblioteca de classes portáteis.

Além desta orientação geral, o Visual Studio também fornece facilidades específicas para reutilizar seus códigos de serviços móveis por vários projetos de aplicativos clientes que são discutidos nas seguintes seções. Para obter informações mais gerais sobre como usar o Visual Studio 2013 para desenvolver aplicativos de plataforma cruzada, consulte [Desenvolvimento de plataforma-cruzada no Visual Studio](http://msdn.microsoft.com/en-us/library/dn771552.aspx).  

### Aplicativos universais Windows

A atualização 2 do Visual Studio 2013 adiciona suporte para os projetos de aplicativos universais do Windows. Os aplicativos universais são soluções que incluem os projetos de aplicativos da Windows Store 8.1 e Windows Phone Store 8.1 juntamente com um projeto de código compartilhado. Neste tipo de projeto, o código compartilhado é tratado como se fosse parte de ambos os projetos Windows Store e Windows Phone. Para obter mais informações, consulte [Criando aplicativos universais do Windows para todos os dispositivos Windows]. Os aplicativos universais do Windows podem ser escritos no C#/XAML e JavaScript/HTML. 

Por padrão, a guia de início rápido no [Portal de Gerenciamento do Azure] gera uma versão universal do aplicativo Windows do aplicativo de exemplo TodoList para ajudar você a começar. Você pode optar por baixar uma versão C#/XAML ou JavaScript/HTML do projeto. Para obter mais informações, consulte [Introdução aos Serviços Móveis](/en-us/documentation/articles/mobile-services-windows-store-get-started/). 

>[AZURE.NOTE]A versão C# do projeto de aplicativos de início rápido do portal compartilha a página code-behing MainPage.xaml.cs mas não usa um modelo de exibição. Para um exemplo do aplicativo TodoList como um projeto de aplicativo universal do Windows no C# que usa o MVVM, veja [Projeto de aplicativo universal do Windows para os Serviços Móveis do Azure usando o MVVM]. 

###<a id="xamarin"></a>Desenvolvimento do Xamarin

Você pode aproveitar a experiência de desenvolvimento do Visual Studio e C# usando o Xamarin e o Visual Studio ou Xamarin Studio para desenvolver aplicativos para o iOS e Android. O Xamarin usa uma implementação entre plataformas do .NET Framework que permite que você use o código C# para desenvolver aplicativos iOS e Android. Ao usar o Xamarin, você pode aproveitar o código existente de seus projetos do Windows que usam a biblioteca cliente do .NET dos Serviços Móveis para acessar seu serviço móvel.  Para obter mais informações, consulte [Desenvolvimento de plataforma cruzada no Visual Studio](http://msdn.microsoft.com/en-us/library/dn771552.aspx).

Para começar a criar aplicativos Xamarin que usam os serviços móveis, consulte os tutoriais de início rápido do Xamarin ([iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started) / [Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started)).


### Aplicativos da Windows Store e Windows Phone Silverlight

No Windows Phone 8.1, você pode escolher usar o XAML anterior baseado no Silverlight para desenvolver aplicativos ou usar o XAML baseado no Tempo de Execução do Windows, que permite o desenvolvimento de aplicativos universais do Windows. Para obter mais informações sobre os aplicativos do Windows Phone 8.1 Silverlight e Windows Phone Store 8.1, consulte [Novidades para os desenvolvedores do Windows Phone 8]. 

A biblioteca cliente do .NET dos Serviços Móveis fornece suporte para os aplicativos do Windows Phone Store 8.1 e Windows Phone Silverlight 8.1. Como os aplicativos do Tempo de Execução do Windows e Windows Phone Silverlight não podem ser criados do mesmo projeto, você deve considerar uma estratégia de reutilização de código, como o PCL e MVVM descritos acima.

>[AZURE.NOTE]Para usar a autenticação de cliente com logon único usando a Conta da Microsoft em aplicativos da Tempo de Execução do Windows e do Windows Phone Silverlight, você deve registrar o aplicativo da Tempo de Execução do Windows primeiro no painel da Windows Store. Isso ocorre porque quando cria um registro do Live Connect para o Windows Phone, você não pode criar um registro para a Windows Store. Para obter mais informações sobre como fazer isso, leia o tópico **Autenticar seu aplicativo Windows Store com logon único do Live Connect** ([Windows Store][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).


<!-- URLs -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Hubs de Notificação do Azure]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[SSO do Windows Store]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO do Windows Phone]: /en-us/develop/mobile/tutorials/single-sign-on-wp8/
[Tutoriais e recursos]: /en-us/develop/mobile/resources/
[Introdução aos Hubs de Notificação]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[Enviar notificações de plataforma cruzada aos usuários]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Introdução ao push de dotnet do Windows]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Introdução ao push de js do Windows]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Introdução ao push do Windows Phone]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8/
[Introdução ao push de iOS]: /en-us/develop/mobile/tutorials/get-started-with-push-ios/
[Introdução ao push do Android]: /en-us/develop/mobile/tutorials/get-started-with-push-android/
[Esquema dinâmico]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
[Como usar um cliente .NET com Serviços Móveis]: en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[objeto de push]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx
[TemplatePushMessage]:http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: /en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/
[Sencha]: /en-us/documentation/articles/partner-sencha-mobile-services-get-started/
[Appcelerator]: /en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[SendAsync]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Novidades para desenvolvedores do Windows Phone 8]: http://msdn.microsoft.com/en-us/library/windows/apps/dn655121(v=vs.105).aspx
[Criando aplicativos universais do Windows para todos os dispositivos Windows]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Projeto de aplicativo universal do Windows para os Serviços Móveis do Azure usando MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de
\n<!--HONumber=42-->
