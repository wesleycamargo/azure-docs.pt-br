<properties
	pageTitle="Chamar uma API personalizada de um cliente da Windows Store - Serviços Móveis"
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo da Windows Store que usa serviços móveis do Azure."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Chamar uma API personalizada do cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo da Windows Store. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador concluído como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Este tutorial baseia-se no início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir um dos tutoriais [Introdução aos Serviços Móveis] ou [Adicionar Serviços Móveis a um aplicativo existente].

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## Próximas etapas

Este tópico explicou como usar o método **InvokeApiAsync** para chamar uma API personalizada bastante simples de seu aplicativo da Windows Store. Para saber mais sobre como usar o método **InvokeApiAsync**, consulte a postagem [API personalizada em Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Além disso, considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Definir uma API personalizada que ofereça suporte a notificações periódicas] <br/>Saiba como usar uma API personalizada para oferecer suporte a notificações periódicas em um aplicativo da Windows Store. Com as notificações periódicas habilitadas, o Windows acessará periodicamente o ponto de extremidade da API personalizada e usará o XML retornado em um formato específico de bloco para atualizar o bloco do aplicativo no menu Iniciar.

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
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md

[Definir uma API personalizada que ofereça suporte a notificações periódicas]: mobile-services-windows-store-dotnet-create-pull-notifications.md
[Scripts de servidor de armazenamento no controle do código-fonte]: mobile-services-store-scripts-source-control.md

<!---HONumber=September15_HO1-->