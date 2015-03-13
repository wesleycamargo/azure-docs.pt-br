<properties 
	pageTitle="Chamar uma API personalizada em um cliente HTML - Serviços Móveis" 
	description="Saiba como definir uma API personalizada e chamá-la em um aplicativo HTML que usa os serviços móveis do Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	writer="jparrel" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Chamar uma API personalizada por meio de um aplicativo HTML

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Este tópico mostra como chamar uma API personalizada em um aplicativo HTML. Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

A API personalizada criada neste tópico fornece a capacidade de enviar uma única solicitação POST que define o sinalizador de conclusão como `true` para todos os itens de tarefas todo na tabela. Sem essa API personalizada, o cliente precisa enviar solicitações individuais para atualizar o sinalizador para cada item todo na tabela.

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para chamar a API personalizada]
3. [Testar o aplicativo]

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados].

## <a name="define-custom-api"></a>Definir a API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>Atualizar o aplicativo para chamar a API personalizada</h2>

1. Usando o editor de texto, abra o arquivo index. HTML, localize o elemento **button** chamado `buttonRefresh`e adicione o seguinte elemento novo logo depois dele: 

		<button id="buttonCompleteAll">Complete All</button> 

	Isso adiciona um novo botão à página. 

2. Em page.js, e após a função **refreshTodoItems**, inclua o código a seguir:

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

	Esse método identifica o evento de **Clique** do novo botão. O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros.

## <a name="test-app"></a>Testar o aplicativo

1. Atualize seu navegador.

2. No aplicativo, digite um texto em **Inserir um TodoItem**e, em seguida, clique em ****.

3. Repita a etapa anterior até que tenha incluído vários itens pendentes na lista.

4. Clique no botão **Concluir Tudo**. É exibida uma caixa de diálogo de mensagem que indica o número de itens marcados como concluídos, e a consulta filtrada é executada novamente, o que limpa todos os itens da lista.

## Próximas etapas

Agora que você criou uma API personalizada e a chamou por meio de seu aplicativo HTML, saiba mais sobre os tópicos de Serviços Móveis a seguir:

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como criar APIs personalizadas.

<!-- Anchors. -->
[Definir a API personalizada]: #define-custom-api
[Atualizar o aplicativo para chamar a API personalizada]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]: #next-steps

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-html-get-started-data
\<!--HONumber=42-->
