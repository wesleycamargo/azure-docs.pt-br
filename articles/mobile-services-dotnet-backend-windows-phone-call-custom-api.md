<properties pageTitle="Call a custom API from a Windows Phone app - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Phone app that use Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Chamar uma API personalizada do cliente

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como chamar uma API personalizada em um aplicativo do Windows Phone. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][] ou [Introdução a dados][]. Para fazer isso, você executará as seguintes etapas:

1.  [Definir a API personalizada][]
2.  [Atualizar o aplicativo para chamar a API personalizada][]
3.  [Testar o aplicativo][]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][] ou [Introdução aos dados][Introdução a dados]. Este tutorial usa o Visual Studio 2012 Express para Windows Phone.

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api][]]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api][]]

## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo da Windows Phone, procure mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Referência de script de servidor de Serviços Móveis][]

    Saiba mais sobre como criar APIs personalizadas.

-   [Scripts de servidor de armazenamento no controle do código-fonte][]

    Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api "Back-end do JavaScript"
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started/
  [Introdução a dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
  [Definir a API personalizada]: #define-custom-api
  [Atualizar o aplicativo para chamar a API personalizada]: #update-app
  [Testar o aplicativo]: #test-app
  [mobile-services-dotnet-backend-create-custom-api]: ../includes/mobile-services-dotnet-backend-create-custom-api.md
  [mobile-services-windows-phone-call-custom-api]: ../includes/mobile-services-windows-phone-call-custom-api.md
  [Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Scripts de servidor de armazenamento no controle do código-fonte]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control
