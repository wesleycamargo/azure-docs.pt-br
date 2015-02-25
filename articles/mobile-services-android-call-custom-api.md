<properties 
	writer="ricksal" 
	pageTitle="Chamar uma API personalizada de um cliente Android | Centro de Desenvolvimento dos Serviços Móveis" 
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo Android que usa os serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/16/2014" 
	ms.author="ricksal,glenga"/>

# Chamar uma API personalizada do cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo Android. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico permite enviar uma única solicitação POST que define o *completed*sinalizador `true` para todos os itens de tarefas pendentes na tabela de Serviços Móveis. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo]


>[AZURE.NOTE] Se você gostaria de ver o código-fonte do aplicativo concluído, clique <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">aqui</a>.

##Pré-requisitos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


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
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-android-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-android-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-android-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-android-get-started-push/

[Scripts de servidor de armazenamento no controle do código-fonte]: /en-us/documentation/articles/mobile-services-store-scripts-source-control
\n<!--HONumber=42-->
