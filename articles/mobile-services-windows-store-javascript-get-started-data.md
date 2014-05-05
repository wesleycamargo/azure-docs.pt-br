<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Introdução a dados" pageTitle="Introdução aos dados (JavaScript do Windows Store) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Saiba como começar a usar os Serviços Móveis para aproveitar os dados em seu aplicativo JavaScript da Windows Store" metaCanonical="https://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Introdução aos dados nos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />


# Começar a trabalhar com dados em serviços móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	


<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Back-end .NET">Back-end .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>


<p>Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar os dados em um aplicativo da Windows Store. Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo que armazena dados na memória, cria um novo serviço móvel, integra o serviço móvel ao aplicativo e faz login no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.</p>

<div class="dev-callout"><b>Observação</b>
<p>Este tutorial requer o Visual Studio 2013, o que torna mais fácil conectar seu aplicativo da Windows Store aos Serviços Móveis. Para concluir o mesmo procedimento básico usando o Visual Studio 2012, siga as etapas no tópico <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js-vs2012/">Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo da Windows Store][Get the Windows Store app] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][Developer Code Samples site], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário deste aplicativo é idêntica ao aplicativo gerado pelo Guia de início rápido dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória.  

1. Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithData do [site de exemplos de código do desenvolvedor]. 

   	![][10]

2. No Visual Studio 2012 Express para Windows 8, abra o projeto baixado, expanda a pasta **js** e analise o arquivo default.js.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um objeto **List** na memória.

3. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][0]  

   	Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

<h2><a name="create-service"></a><span class="short-header">Criar o serviço móvel</span>Criar um novo serviço móvel a partir do Visual Studio</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>No Gerenciador de Soluções, expanda as pastas **serviços**, **serviços móveis**, **&lt;seu_serviço&gt;**, abra o arquivo de script service.js e observe a nova variável global, que se parece com o exemplo a seguir:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma variável global. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Como uma referência a esse script foi adicionada ao arquivo default.html, esta variável está disponível para todos os arquivos de script que também fazem referência desta página.</p>
</li>
</ol>

<h2><a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicionar uma nova tabela ao serviço móvel e atualizar o aplicativo</h2>

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

6. No arquivo de script default.js, comente a linha que define a coleção de itens existentes e remova os comentários ou adicione as seguintes linhas de código e substitua o campo `<yourClient>;` pela variável adicionada ao arquivo service.js quando você conectou seu projeto ao serviço móvel:

		var todoTable = <yourClient>.getTable('TodoItem');

   	Esse código criará um objeto proxy (**todoTable**) para a nova tabela de banco de dados. 

7. Substitua a função **InsertTodoItem** pelo seguinte código:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Esse código insere um novo item na tabela.

	<div class="dev-callout"><strong>Observação</strong><p>Novas tabelas são criadas com uma coluna de identificação. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx">Esquema dinâmico</a>.</p></div>

8. Substitua a função **RefreshTodoItems** pelo seguinte código:

		var refreshTodoItems = function () {
		    // This code refreshes the entries in the list by querying the table. 
		    todoTable.read().done(function (results) {
		        todoItems = new WinJS.Binding.List(results);
		        listItems.winControl.itemDataSource = todoItems.dataSource;
		    });
		};

   	Isso define a associação à coleção de itens em todoTable, que contém todos os objetos **TodoItem** retornados do serviço móvel. 

9. Substitua a função **UpdateCheckedTodoItem** pelo seguinte código:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

   	Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

<h2><a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo com o novo serviço móvel</h2>

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e clique em **Salvar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e clique no seu serviço móvel.

4. Clique na guia **Dados** e clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5. No aplicativo, verifique um dos itens da lista e retorne À guia Procurar no portal e clique em **Atualizar**. 

  	Observe que o valor completo foi alterado de **false** para **true**.

6. No arquivo de projeto default.js, substitua a função existente **RefreshTodoItems** pelo seguinte código que filtra os itens concluídos:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. No aplicativo, verifique um outro item na lista e clique no botão **Atualizar**.

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

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual de tutorial do HTML/JavaScript de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.

<!-- Anchors. -->

[Baixar o aplicativo da Windows Store]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-js
[Introdução aos Serviços Móveis]: ./mobile-services-get-started.md
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[C# e XAML]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referência conceitual de tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/

