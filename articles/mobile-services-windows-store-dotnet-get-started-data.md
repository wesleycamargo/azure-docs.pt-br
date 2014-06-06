<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Começar a trabalhar com dados" pageTitle="Começar a trabalhar com dados (Windows Store) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Saiba como começar a usar os serviços móveis para aproveitar os dados em seu aplicativo da Windows Store" metaCanonical="" services="" documentationCenter="Mobile" title="Começar a trabalhar com dados em serviços móveis" authors=""  solutions="" writer="glenga" manager="" editor="" />



# Começar a trabalhar com dados em serviços móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Back-end do .NET">Back-end do .NET</a> | 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>


<p>Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar os dados em um aplicativo da Windows Store. Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo que armazena dados na memória, cria um novo serviço móvel, integra o serviço móvel ao aplicativo e faz login no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.</p>

<div class="dev-callout"><b>Observação</b>
<p>Este tutorial requer o Visual Studio 2013, o que torna mais fácil conectar seu aplicativo da Windows Store aos Serviços Móveis. Para concluir o mesmo procedimento básico usando o Visual Studio 2012, siga as etapas no tópico <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/">Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Faça o download do projeto de aplicativo da Windows Store][Get the Windows Store app] 
2. [Criar o serviço móvel no Visual Studio]
3. [Adicionar uma tabela de dados para armazenamento e atualização do aplicativo]
5. [Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, será possível criar uma conta de avaliação gratuita em questão de minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][Developer Code Samples site], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário deste aplicativo é idêntica ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória. 

1. Baixe a versão C# do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor]. 

   	![][10]

2. No Visual Studio 2013, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection&lt;TodoItem&gt;** na memória.

3. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	![][0]  

   	Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

<h2><a name="create-service"></a><span class="short-header">Criar o serviço móvel</span>Criar um novo serviço móvel a partir do Visual Studio</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7"><li><p>No Solution Explorer, abra o arquivo de código App.xaml.cs e observe o novo campo estático que foi adicionado à classe **aplicativo**, que parece com o exemplo a seguir:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma instância da <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Este campo estático está disponível para todas as páginas em seu aplicativo.</p>
</li>
</ol>

<h2><a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicionar uma nova tabela ao serviço móvel e atualizar o aplicativo</h2>

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

3. No arquivo MainPage.xaml.cs, adicione ou remova o comentário dos seguintes usando instruções: 

		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

4. Nesse mesmo arquivo, substitua a definição da classe TodoItem pelo seguinte código: 

		public class TodoItem
		{
		    public string Id { get; set; }
		
		    [JsonProperty(PropertyName = "text")]
		    public string Text { get; set; }
		
		    [JsonProperty(PropertyName = "complete")] 
		    public bool Complete { get; set; }
		}

	O **JsonPropertyAttribute** é usado para definir o mapeamento entre os nomes das propriedades no tipo de cliente para nomes de coluna na tabela de dados subjacente.

5. Comente a linha que define a coleção de itens existentes e, em seguida, remova os comentários ou adicione as seguintes linhas e substitua o campo _&lt;yourClient&gt;_ pelo campo `MobileServiceClient` adicionado ao arquivo App.xaml.cs quando você conecta seu projeto ao serviço móvel: 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Esse código cria uma coleção de ligação com reconhecimento de serviços móveis (itens) e uma classe de proxy para a tabela de banco de dados (todoTable). 

6. No método **InsertTodoItem**, remova a linha de código que define a propriedade **TodoItem.Id**, adicione o modificador **async** ao método e remova o comentário da seguinte linha de código: 

		await todoTable.InsertAsync(todoItem);


	Esse código insere um novo item na tabela. 

	<div class="dev-callout"><strong>Observação</strong><p>novas tabelas são criadas com as colunas Id, __createdAt, __updatedAt e __version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx">Esquema dinâmico</a>.</p></div>

7. No método **RefreshTodoItems**, adicione o modificador **async** ao método e, em seguida, remova o comentário da linha de código a seguir: 

		items = await todoTable.ToCollectionAsync();

	Isso define a associação à coleção de itens em `todoTable`, que contém todos os objetos **TodoItem** retornados do serviço móvel. 

8. No método **UpdateCheckedTodoItem**, adicione o modificador **async** ao método e remova o comentário da linha de código a seguir: 

		await todoTable.UpdateAsync(item);

	Isso envia uma atualização de item para o serviço móvel. 

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo com o novo serviço móvel</h2>

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5. No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**. 

  	Observe que o valor completo foi alterado de **false** para **true**.

6. No arquivo de projeto MainPage.xaml.cs, substitua o método existente **RefreshTodoItems** pelo seguinte código que filtra os itens concluídos:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7. No aplicativo, verifique um outro item na lista e, em seguida, clique no botão **Atualizar**.

   	Observe que o item marcado agora desaparecerá da lista. Cada atualização resulta em uma viagem para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar os dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Aprenda a usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações de push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual de tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
  
<!-- Anchors. -->

[Baixar o aplicativo da Windows Store]: #download-app
[Criar o serviço móvel no Visual Studio]: #create-service
[Adicionar uma tabela de dados para armazenamento e atualização do aplicativo]: #add-table
[Atualizar o aplicativo para usar Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png


<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações de push]: ../mobile-services-windows-store-dotnet-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-data-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referência conceitual de tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Classe de MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

