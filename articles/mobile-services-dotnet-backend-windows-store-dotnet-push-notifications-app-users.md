<properties linkid="/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Enviar notificações por push para usuários autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="C# da Windows Store" class="current">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como enviar as notificações por push a um usuário de autenticação em qualquer dispositivo registrado. Diferente do tutorial anterior de [notificação por push][notificação por push], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID do usuário atribuída. Finalmente, o código do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

-   [Atualizar o serviço para solicitar autenticação para registro][Atualizar o serviço para solicitar autenticação para registro]
-   [Atualizar o aplicativo para fazer logon antes do registro][Atualizar o aplicativo para fazer logon antes do registro]
-   [Testar o aplicativo][Testar o aplicativo]

Este tutorial é compatível com aplicativos da Windows Store e da Loja do Windows Phone.

## Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Adiciona um requisito de logon ao aplicativo de amostra TodoList.

-   [Introdução às notificações por push][notificação por push]
    Configura o aplicativo de amostra TodoList para notificações por push usando Hubs de Notificação.

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

## <a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][mobile-services-dotnet-backend-push-notifications-app-users]]

## <a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users][mobile-services-windows-store-dotnet-push-notifications-app-users]]

## <a name="test"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-windows-test-push-users][mobile-services-windows-test-push-users]]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->
<!-- Anchors. -->
<!-- URLs. -->

  [C\# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/ "Back-end do JavaScript"
  [notificação por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Atualizar o serviço para solicitar autenticação para registro]: #register
  [Atualizar o aplicativo para fazer logon antes do registro]: #update-app
  [Testar o aplicativo]: #test
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-windows-store-dotnet-push-notifications-app-users]: ../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md
  [mobile-services-windows-test-push-users]: ../includes/mobile-services-windows-test-push-users.md
