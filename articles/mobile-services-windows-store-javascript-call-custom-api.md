<properties linkid="mobile-services-call-custom-api-js" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store JS client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Chamar uma API personalizada do cliente

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-call-custom-api"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como chamar uma API personalizada em um aplicativo da Windows Store. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução a dados][Introdução a dados]. Para fazer isso, você executará as seguintes etapas:

1.  [Definir a API personalizada][Definir a API personalizada]
2.  [Atualizar o aplicativo para chamar a API personalizada][Atualizar o aplicativo para chamar a API personalizada]
3.  [Testar o aplicativo][Testar o aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos dados][Introdução a dados]. Este tutorial usa o Visual Studio 2012 Express para Windows 8.

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo da Windows Store, procure mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Definir uma API personalizada que ofereça suporte a notificações periódicas][Definir uma API personalizada que ofereça suporte a notificações periódicas]
    Saiba como usar uma API personalizada para oferecer suporte a notificações periódicas em um aplicativo da Windows Store. Com as notificações periódicas habilitadas, o Windows acessará periodicamente o ponto de extremidade da API personalizada e usará o XML retornado em um formato específico de bloco para atualizar o bloco do aplicativo no menu Iniciar.

-   [Referência de script de servidor de Serviços Móveis][Referência de script de servidor de Serviços Móveis]
    Saiba mais sobre como criar APIs personalizadas.

-   [Scripts de servidor de armazenamento no controle do código-fonte][Scripts de servidor de armazenamento no controle do código-fonte]
    Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. --> 
<!-- URLs. -->

  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
  [Introdução a dados]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
  [Definir a API personalizada]: #define-custom-api
  [Atualizar o aplicativo para chamar a API personalizada]: #update-app
  [Testar o aplicativo]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-windows-store-javascript-call-custom-api]: ../includes/mobile-services-windows-store-javascript-call-custom-api.md
  [Definir uma API personalizada que ofereça suporte a notificações periódicas]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
  [Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Scripts de servidor de armazenamento no controle do código-fonte]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control
