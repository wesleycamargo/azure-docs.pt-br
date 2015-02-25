<properties 
	pageTitle="Chamar uma API personalizada em um aplicativo iOS | Serviços Móveis" 
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo iOS que usa os serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	writer="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Chamar uma API personalizada do cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo iOS. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador de conclusão como `true` para todos os itens todo na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução a dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. Este tutorial requer o [SDK do iOS para Serviços Móveis](https://go.microsoft.com/fwLink/p/?LinkID=266533), o [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) e o iOS 5.0 ou posterior.

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Próximas etapas

Agora que você criou uma API personalizada e a chamou em seu aplicativo iOS, procure mais informações sobre os seguintes tópicos de Serviços Móveis:

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
[Notificações por push e Live Connect do Windows]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-ios-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-ios-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-ios-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-ios-get-started-push/
[Scripts de servidor de armazenamento no controle do código-fonte]: /en-us/documentation/articles/mobile-services-store-scripts-source-control
\n<!--HONumber=42-->
