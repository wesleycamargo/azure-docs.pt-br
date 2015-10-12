<properties
	pageTitle="Chamar uma API personalizada em um cliente HTML - Serviços Móveis"
	description="Saiba como definir uma API personalizada e chamá-la de um aplicativo HTML que usa os Serviços Móveis do Azure."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"  
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="glenga"/>

# Chamar uma API personalizada a partir de um aplicativo HTML

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada a partir de um aplicativo HTML. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador concluído como `true` para todos os itens de tarefas pendentes na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Este tutorial baseia-se no início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir um dos tutoriais [Introdução aos Serviços Móveis] ou [Adicionar Serviços Móveis a um aplicativo existente].

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada

1. Usando seu editor de texto, abra o arquivo index.html, localize o elemento **botão** chamado `buttonRefresh` e adicione o novo elemento abaixo logo depois dele:

		<button id="buttonCompleteAll">Complete All</button>

	Isso adiciona um novo botão à página.

2. Em page.js e após a função **refreshTodoItems**, inclua o código a seguir:

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	Esse método identifica o evento **Click** do novo botão. O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros.

## <a name="test-app"></a>Testar o aplicativo

1. Atualize seu navegador.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

3. Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.

4. Clique no botão **Concluir Tudo**. É exibida uma caixa de diálogo de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

## Próximas etapas

Esse tópico mostrou como usar a função **invokeApi** para chamar uma API personalizada bastante simples de seu aplicativo HTML/JavaScript. Para saber mais sobre como usar a função **invokeApi**, confira a postagem [API personalizada nos Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Além disso, considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Referência de script de servidor de Serviços Móveis]<br/>Saiba mais sobre como criar APIs personalizadas.

* [Scripts de servidor de armazenamento no controle do código-fonte] <br/>Saiba como usar o recurso de controle de origem para desenvolver e publicar o código de script da API personalizada mais facilmente e de maneira mais segura.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Serviços Móveis]: mobile-services-html-get-started.md
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-html-get-started-data.md
[Scripts de servidor de armazenamento no controle do código-fonte]: mobile-services-store-scripts-source-control.md

<!---HONumber=Oct15_HO1-->