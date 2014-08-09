<properties linkid="" urlDisplayName="" pageTitle="Adicionar paginação aos dados (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Saiba como usar a paginação para gerenciar a quantidade de dados retornados ao seu aplicativo Xamarin iOS dos Serviços Móveis." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refinar as consultas dos Serviços Móveis com paginação" documentationCenter="Mobile" />


# Refinar consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>


Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos Serviços Móveis do Azure. Neste tutorial, você usará as propriedades de consulta **Skip** e **Take** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observação</b>
<p>Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você pode solicitar explicitamente até 1.000 itens na resposta.</p>
</div>

Este tutorial baseia-se nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados]. 

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

    <div class="dev-callout"><b>Observação</b>
    <p>Este tutorial usa um cenário simplificado, definindo valores de paginação embutidos em código para as propriedades <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode chamar o método <strong>IncludeTotalCount</strong> para obter a contagem total de todos os itens disponíveis no servidor, junto com os dados paginados.</p>
    </div>

## <a name="next-steps"> </a>Próximas Etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Se desejar obter mais informações sobre o seguinte tópico de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.
 
<!--
* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
-->

<!-- Anchors. -->

[Próximas Etapas]:#próximas etapas

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: ./pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portal de Gerenciamento]: https://manage.windowsazure.com/

