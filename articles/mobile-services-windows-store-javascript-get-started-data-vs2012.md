<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar os dados em um aplicativo da Windows Store. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial adiciona a funcionalidade dos Servi&ccedil;os M&oacute;veis ao um aplicativo da Windows Store criado no Visual Studio 2012. O Visual Studio 2013 inclui novos recursos que tornam mais f&aacute;cil conectar seu aplicativo da Windows Store ao Servi&ccedil;os M&oacute;veis. Para obter mais informa&ccedil;&otilde;es, consulte <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js/">Introdu&ccedil;&atilde;o aos dados nos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo da Windows Store. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1.  Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithData do [site de exemplos de código do desenvolvedor][aplicativo GetStartedWithData].

    ![][]

2.  No Visual Studio 2012 Express para Windows 8, abra o projeto baixado, expanda a pasta **js** e analise o arquivo default.js.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um objeto **List** na memória.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][1]

    Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo, selecione a categoria **Online**, pesquise por `WindowsAzure.MobileServices.WinJS`, clique em **Instalar** no pacote de **Serviços Móveis do Azure para WinJS** e aceite o contrato de licença.

    ![][2]

    Isso adiciona a biblioteca de cliente dos Serviços Móveis ao projeto.

3.  No arquivo default.html do projeto, adicione a seguinte referência de script ao cabeçalho da página:

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

5.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][3]

    Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

6.  No Visual Studio, abra o arquivo default.js, remova o comentário do código a seguir que define a variável **client** e forneça a URL e a chave do aplicativo do serviço móvel no construtor **MobileServiceClient**, nesta ordem.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel.

7.  Remova os comentários da linha de código a seguir:

        var todoTable = client.getTable('TodoItem');

    Esse código criará um objeto proxy (**todoTable**) para a nova tabela de banco de dados.

8.  Substitua a função **InsertTodoItem** pelo seguinte código:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Esse código insere um novo item na tabela.

9.  Substitua a função **RefreshTodoItems** pelo seguinte código:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    Isso define a associação à coleção de itens na todoTable, que contém todos os itens concluídos retornados do serviço móvel.

10. Substitua a função **UpdateCheckedTodoItem** pelo seguinte código:

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][4]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5.  No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**.

    Observe que o valor completo foi alterado de **false** para **true**.

6.  No arquivo de projeto default.js, substitua a função existente **RefreshTodoItems** pelo seguinte código que filtra os itens concluídos:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  No aplicativo, verifique um outro item na lista e, em seguida, clique no botão **Atualizar**.

    Observe que o item marcado agora desaparecerá da lista. Cada atualização resulta em uma viagem para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-data-js-vs2012 "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /pt-br/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /pt-br/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Introdução aos dados nos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-with-data-js/
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-js
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012
