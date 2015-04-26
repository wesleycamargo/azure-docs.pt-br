<properties 
	pageTitle="Adicionar paginação aos dados (Xamarin iOS) | Centro de desenvolvimento móvel" 
	description="Saiba como usar paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS Xamarin dos serviços móveis." 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# Refinar as consultas dos Serviços Móveis com paginação

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos Serviços Móveis do Azure. Neste tutorial, você usará as propriedades de consulta **Skip** e **Take** no cliente para solicitar "páginas" de dados específicas.

> [AZURE.NOTE] Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados]. 

1. No Xamarin Studio, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo, insira texto na caixa de texto e clique no ícone de sinal de adição (**+**).

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem. 

4. Abra o arquivo **TodoService.cs**, localize o método **RefreshDataAsync** e substitua a consulta LINQ em <code>todoTable</code> pela seguinte consulta: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos. 

5. Recrie e inicie o aplicativo. 
   
    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos. 

7. Atualize a consulta LINQ no método **RefreshDataAsync** mais uma vez para o seguinte:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   Neste momento, defina o valor de **Skip** para 3. 

   Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    > [AZURE.NOTE] Este tutorial usa um cenário simplificado, definindo valores de paginação embutidos em código para as propriedades **Take** e **Skip**. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode chamar o método **IncludeTotalCount** para obter a contagem total de todos os itens disponíveis no servidor, junto com os dados paginados.

## <a name="next-steps"></a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre o seguinte tópico de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.
 
<!--
* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
-->

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: ./pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portal de Gerenciamento]: https://manage.windowsazure.com/



<!--HONumber=42-->
