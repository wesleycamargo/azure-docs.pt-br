<properties 
	pageTitle="Chamar uma API personalizada de um aplicativo do Windows Phone - Serviços Móveis" 
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo do Windows Phone que usa os serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Chamar uma API personalizada do cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo do Windows Phone. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador concluído como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Este tutorial é baseado no exemplo GetStartedWithData, um aplicativo TodoList simples. Antes de iniciar este tutorial, você deve primeiro concluir [Adicionar Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]


## Próximas etapas

Este tópico mostrou como usar o método **InvokeApiAsync** para chamar uma API personalizada bastante simples de seu aplicativo Windows Phone. Para saber mais sobre como usar o método **InvokeApiAsync**, consulte a postagem [API personalizada em Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Além disso, considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Referência de script de servidor de Serviços Móveis]<br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte] <br/>Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Scripts de servidor de armazenamento no controle do código-fonte]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO1-->