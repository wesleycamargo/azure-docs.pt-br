<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Introdução à sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Este tutorial abrange o recurso de sincronização offline dos Serviços Móveis, o qual permite gravar aplicativos utilizáveis quando o usuário final não tem acesso à rede.

A sincronização offline possui diversos usos possíveis:

-   Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
-   Torna os aplicativos resilientes em relação à conectividade da rede intermitente
-   Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
-   Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos

Este tutorial mostra como atualizar o aplicativo com base nos tutoriais [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos Dados][Introdução aos Dados] para oferecer suporte aos recursos offline dos Serviços Móveis do Azure. Você irá adicionar dados em um cenário desconectado offline, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE] Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

## Obter o aplicativo de amostra

**Um tutorial completo estará disponível em breve. Enquanto isso, consulte a amostra [Lista de tarefas pendentes offline do iOS][Lista de tarefas pendentes offline do iOS] e [a postagem complementar no blog][a postagem complementar no blog].**

<!-- ## Next steps  * [How to use the Mobile Services client library for iOS]    -->
<!-- URLs. -->

  [C\# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "C# da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /pt-br/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-ios-get-started/
  [Introdução aos Dados]: /pt-br/documentation/articles/mobile-services-ios-get-started-data/
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [Lista de tarefas pendentes offline do iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [a postagem complementar no blog]: http://aka.ms/iosoffline
