<properties writer="ricksal" pageTitle="Chamar uma API personalizada de um cliente Android | Centro de Desenvolvimento dos Serviços Móveis" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="ricksal,glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# Chamar uma API personalizada do cliente

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo Android. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico permite enviar uma única solicitação POST que define o sinalizador de *concluído* como `true` para todos os itens de tarefas pendentes na tabela de Serviços Móveis. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados].Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo] 


>[AZURE.NOTE] Se você quiser ver o código-fonte do aplicativo concluído, vá <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">aqui</a>.

##Pré-requisitos

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definir a API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo Android, procure mais informações sobre os seguintes tópicos de Serviços Móveis:



* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte]
  <br/> Saiba como usar o recurso de controle do código-fonte para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Definir a API personalizada]: #define-custom-api
[Atualizar o aplicativo para chamar a API personalizada]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]: #next-steps

<!-- URLs. -->
[SDK para Android de Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-android-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-android-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-android-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-android-get-started-push/

[Scripts de servidor de armazenamento no controle do código-fonte]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=35.1-->
