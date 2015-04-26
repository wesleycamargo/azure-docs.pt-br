<properties 
	pageTitle="Adicionar paginação aos dados (Android) | Mobile Dev Center" 
	description="Saiba como usar paginação para gerenciar a quantidade de dados retornados ao seu aplicativo Android dos serviços móveis." 
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
	ms.date="09/25/2014" 
	ms.author="ricksal"/>

# Refinar as consultas dos Serviços Móveis com paginação

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo Android dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **top** e **skip** no cliente para solicitar "páginas" de dados específicas.

> [AZURE.NOTE] Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Esse tutorial baseia-se nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial sobre como trabalhar com série de dados [Introdução aos dados]. 

1. No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos dados].

2. No menu **Executar**, clique em **Executar** para iniciar o aplicativo, digite texto na caixa de texto e clique no botão **Adicionar**.

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem. 

4. No arquivo ToDoActivity.java, substitua o método **RefreshTodoItems** pelo seguinte código:

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

  	Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5. Recrie e inicie o aplicativo. 
   Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos. 

6. (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. 

   	Observe que o método `top(3)` foi convertido na opção de consulta `$top=3` no URI da consulta.

7. Atualize o método **RefreshTodoItems** mais uma vez com o código a seguir:         
		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.
> [AZURE.NOTE] Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos **top**e **skip**. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode chamar o método **includeInlineCount** para obter a contagem total dos itens disponíveis no servidor, juntamente com os dados paginados.
8. (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel. 

   	Observe que o método `skip(3)` foi convertido na opção de consulta `$skip=3` no URI de consulta.

## <a name="next-steps"> </a>Próximas Etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.
 
* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android

[Portal de Gerenciamento]: https://manage.windowsazure.com/




<!--HONumber=42-->
