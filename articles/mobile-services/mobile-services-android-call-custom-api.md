<properties 
	pageTitle="Chamar uma API personalizada de um cliente Android | Microsoft Azure" 
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo Android que usa os serviços móveis do Azure." 
	services="mobile-services" 
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
	ms.date="06/01/2015" 
	ms.author="ricksal"/>

# Chamar uma API personalizada do cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo Android. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico permite enviar uma única solicitação POST que define o sinalizador de *concluído* para `true` para todos os itens de tarefas pendentes na tabela dos Serviços Móveis. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução a dados].


>[AZURE.NOTE]Se você gostaria de ver o código-fonte do aplicativo concluído, clique <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">aqui</a>.

##Pré-requisitos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


## Próximas etapas

Este tópico mostrou como usar o método **invokeApi** para chamar uma API personalizada bastante simples de seu aplicativo Android. Para saber mais sobre como usar o método **invokeApi**, consulte a postagem [API personalizada nos Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Além disso, considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Referência de script de servidor de Serviços Móveis]<br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte] <br/>Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: mobile-services-android-get-started.md
[Introdução a dados]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: ../mobile-services-android-get-started-push.md

[Scripts de servidor de armazenamento no controle do código-fonte]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=August15_HO8-->