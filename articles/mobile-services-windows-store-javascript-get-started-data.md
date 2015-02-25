<properties 
	pageTitle="Introdução aos dados (JavaScript da Windows Store) | Centro de desenvolvimento de Serviços Móveis" 
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo JavaScript da Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>	

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Back-end do .NET</a> | 
	<a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Back-end do JavaScript</a>
</div>


Este tópico mostra como usar os Serviços Móveis do Azure para utilizar os dados em um aplicativo da Windows Store. Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo que armazena dados na memória, cria um novo serviço móvel, integra o serviço móvel ao aplicativo e faz login no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

>[AZURE.NOTE]Este tópico mostra como usar o Visual Studio 2013 para adicionar os Serviços Móveis do Azure em um projeto da Windows Store. Você pode usar o mesmo serviço móvel de back-end do JavaScript para um projeto de aplicativo universal do Windows. Para obter mais informações, consulte a [versão do aplicativo universal do Windows](/en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data) deste tutorial.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo da Windows Store][Obter o aplicativo da Windows Store] 
2. [Criar o serviço móvel]
3. [Adicionar uma tabela de dados para armazenamento]
4. [Atualizar o aplicativo para usar os Serviços Móveis]
5. [Testar o aplicativo com os Serviços Móveis]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, o que torna mais fácil conectar seu aplicativo da Windows Store aos Serviços Móveis. Para concluir o mesmo procedimento básico usando o Visual Studio 2012, siga as etapas no tópico <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][Site de Exemplos de Código do Desenvolvedor], que é um aplicativo de projeto da Windows Store em Visual Studio 2013. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.  

1. Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithData do [site de exemplos de código do desenvolvedor]. 

2. No Visual Studio 2012 Express para Windows 8, abra o projeto baixado, expanda a pasta **js** e analise o arquivo default.js.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um objeto **List** na memória.

3. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	![][0]  

   	Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

##<a name="create-service"></a>Criar um novo serviço móvel por meio do Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>No Gerenciador de Soluções, expanda as pastas **serviços**, **serviços móveis**, **&lt;seu_serviço&gt;**, abra o arquivo de script service.js e observe a nova variável global, que se parece com o exemplo a seguir:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma variável global. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Como uma referência a esse script foi adicionada ao arquivo default.html, esta variável está disponível para todos os arquivos de script que também fazem referência desta página.</p>
</li>
</ol>

##<a name="add-table"></a>Adicionar uma nova tabela para armazenamento de dados

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Novas tabelas são criadas com as colunas Id, __createdAt, __updatedAt e __version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5. No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**. 

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

7. No aplicativo, verifique um outro item na lista e, em seguida, clique no botão **Atualizar**.

   	Observe que o item marcado agora desaparecerá da lista. Cada atualização resulta em uma viagem para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual do tutorial HTML/JavaScript de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.

<!-- Anchors. -->

[Baixar o aplicativo da Windows Store]: #download-app
[Criar o serviço móvel]: #create-service
[Adicionar uma tabela de dados para armazenamento]: #add-table
[Atualizar o aplicativo para usar os Serviços Móveis]: #update-app
[Testar o aplicativo com os Serviços Móveis]: #test-app
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validar e modificar dados com scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refinar consultas com paginação]: /en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referência conceitual do tutorial HTML/JavaScript de Serviços Móveis]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library/
\n<!--HONumber=42-->
