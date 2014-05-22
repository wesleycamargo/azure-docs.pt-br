<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Introdução aos dados" pageTitle="Introdução aos dados (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como começar a usar os Serviços Móveis para utilizar os dados em seu aplicativo da Windows Store" metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos dados nos Serviços Móveis" authors="wesmc" solutions="" manager="" editor="" />



# Começar a trabalhar com dados em serviços móveis


<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo da Windows Store. Neste tutorial, você baixará um projeto do Visual Studio 2013 para um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial oferecerá suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

<div class="dev-callout"><b>Observação</b>
<p>Este tutorial requer o Visual Studio 2013.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo da Windows Store]
2. [Criar um novo serviço móvel]
3. [Baixar o serviço móvel localmente]
4. [Atualizar o aplicativo da Windows Store para usar o Serviço Móvel]
5. [Testar o aplicativo da Windows Store no serviço hospedado localmente]
6. [Publicar o serviço móvel no Azure]
7. [Testar o aplicativo da Windows Store no serviço hospedado no Azure]


<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][Developer Code Samples site], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário desse aplicativo é semelhante ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória. 

1. Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor]. 

   	![][1]

2. Execute o Visual Studio 2013 com privilégios de administrador clicando com o botão direito do mouse em Visual Studio e clicando em **Executar como administrador**.

3. No Visual Studio 2013, abra o projeto baixado. Expanda a pasta js no Gerenciador de Soluções e examine o arquivo default.js.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um `WinJS.Binding.List` na memória.

4. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

5. No aplicativo, digite um texto na caixa abaixo de **Inserir um TodoItem** e clique em **Salvar**.

   	![][0]  

   	Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

<h2><a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Criar um novo serviço móvel</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]



<h2><a name="download-the-service-locally"></a><span class="short-header">Baixar o serviço localmente</span>Baixar o projeto do serviço móvel e adicioná-lo à solução</h2>

1. No [Portal de Gerenciamento do Azure], clique em seu novo Serviço Móvel ou na guia do ícone de nuvem para ir para a página de visão geral.

    ![][2]

2. Clique na plataforma **Windows Store**. Na seção **Introdução**, expanda **Conectar a um aplicativo da Windows Store existente** e clique no botão **Baixar** para baixar um projeto inicial para o serviço móvel. 

    ![][3]

3. Role até a parte inferior da seção **Introdução** para a etapa intitulada **Publicar seu serviço na nuvem**. Clique no link mostrado na captura de tela abaixo para baixar um arquivo de perfil de publicação para o serviço móvel que você acabou de baixar.

    > [WACOM.NOTE] Salve o arquivo em um local seguro porque ele contém informações confidenciais relativas à sua conta do Azure. Você excluirá o arquivo após a publicação do serviço móvel mais tarde neste tutorial. 

    ![][5]


4. Descompacte o projeto inicial do serviço personalizado baixado. Copie as pastas que estavam no arquivo zip no mesmo diretório do **JavaScript** onde o arquivo da solução Introdução aos dados (.sln) está localizado. Isso facilita a manutenção da sincronização de todos os pacotes pelo Gerenciador de Pacotes NuGet. 

    ![][26]

5. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em sua solução para o aplicativo de Introdução aos dados da Windows Store. Clique em **Adicionar** e em **Projeto Existente**.

    ![][4]

6. Na caixa de diálogo Adicionar Projeto Existente, navegue para a pasta do projeto do serviço móvel que você moveu para o diretório do **JavaScript**. Selecione o arquivo do projeto C# (.csproj) no subdiretório do serviço. Clique em **Abrir** para adicionar o projeto à sua solução.

    ![][6]

7. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço que você acabou de adicionar e clique em **Compilar** para verificar se ele é compilado sem erros. Durante a compilação, o Gerenciador de Pacotes NuGet talvez precise restaurar alguns pacotes NuGet referenciados no projeto.

    ![][20]

8. Clique com o botão direito do mouse no projeto do serviço novamente. Desta vez, clique em **Iniciar nova instância** na caixa de contexto **Depurar**.

    ![][21]

    O Visual Studio abre a página da web padrão do serviço. Você pode clicar em **Experimentar agora** para testar métodos no serviço móvel da página da web padrão.

    ![][22]

    Por padrão, o Visual Studio hospeda o serviço móvel localmente no IIS Express. Você pode ver isso, clicando com o botão direito do mouse no ícone de bandeja do IIS Express na barra de tarefas.

    ![][23]


<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo da Windows Store</span>Atualizar o aplicativo da Windows Store para usar o serviço móvel</h2>

Nesta seção, você irá atualizar o aplicativo da Windows Store para usar o serviço móvel como um serviço de back-end do aplicativo.


1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store e clique em **Gerenciar Pacotes NuGet**.

    ![][7]

2. Na caixa de diálogo Gerenciar Pacotes NuGet, pesquise por **WindowsAzure.MobileServices.WinJS** na coleção de pacotes online e clique para instalar o Pacote NuGet dos Serviços Móveis do Azure. Em seguida, feche a caixa de diálogo.

    ![][8]

3. De volta no Portal de Gerenciamento do Azure, na página de visão geral de seu serviço móvel, localize a etapa rotulada **Conectar seu aplicativo e armazenar dados no seu serviço**. Clique em **JavaScript** como sua linguagem e copie o trecho de código para criar o `MobileServiceClient`

    ![][9]

4. No Gerenciador de Soluções do Visual Studio, expanda a pasta **js** e abra o default.js. Cole o trecho de código que você copiou no manipulador de eventos `app.onactivated` imediatamente antes da definição da variável `todoItems`. Observe que o trecho de código inclui um construtor comentado para conexão com o serviço móvel no Azure usando a chave do aplicativo. Você irá remover esse comentário em uma etapa posterior.

    ![][10]


5. No default.js, substitua o restante do código do manipulador de eventos `app.onactiviated` pelo seguinte código que define `todoItems` e as operações que testaremos com o serviço móvel. Em seguida, salve o arquivo.

    Esse código usa o SDK dos Serviços Móveis para JavaScript para permitir que o aplicativo armazene seus dados em uma tabela fornecida pelo serviço em vez de localmente na memória. Os três métodos principais são `insertTodoItem`, `refreshTodoItems` e `updateCheckedTodoItem`. Esses três métodos permitem que você insira, consulte e atualize sua coleção de dados, de forma assíncrona em uma tabela no Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. No Gerenciador de Soluções do Visual Studio, abra o arquivo default.js. Na parte superior do arquivo, adicione uma nova referência de script WinJS para MobileServices.js. Em seguida, salve o arquivo.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]



<h2><a name="test-locally-hosted"></a><span class="short-header">Testar o aplicativo da Windows Store localmente</span>Testar o aplicativo da Windows Store com o serviço hospedado localmente</h2>

Nesta seção você usará o Visual Studio para hospedar o aplicativo e o serviço móvel localmente em sua estação de trabalho de desenvolvimento no IIS Express. Em seguida, você testará o aplicativo e o serviço de back-end.


1. No Visual Studio, pressione a tecla F7 ou clique em **Compilar solução** no menu Compilar para compilar o aplicativo da Windows Store e o serviço móvel. Verifique se os dois projetos são compilados sem erros na janela de saída do Visual Studio

    ![][11]

2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar para executar o aplicativo e hospedar o serviço móvel localmente no IIS Express. 

    ![][12]


3. Digite um novo texto de todoitem. Em seguida, clique em **Salvar**. Isso insere um novo todoItem no banco de dados criado pelo serviço móvel hospedado localmente no IIS Express. 

    ![][13]

4. Clique na caixa de seleção de um dos itens para marcá-lo como concluído.

    ![][15]

5. No Visual Studio, você pode exibir as alterações no banco de dados criado para o serviço de back-end abrindo o Gerenciador de Servidores e expandindo as Conexões de Dados. Clique com o botão direito do mouse na tabela TodoItems em **MS_TableConnectionString** e clique em **Mostrar Dados da Tabela**

    ![][14]



<h2><a name="publish-mobile-service"></a><span class="short-header">Publicar o serviço móvel no Azure</span>Publicar o serviço móvel no Azure</h2>


[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<h2><a name="test-azure-hosted"></a><span class="short-header">Testar o serviço móvel no Azure</span>Testar o serviço móvel publicado no Azure</h2>

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **js** e abra o default.js.  Comente o código que cria o `MobileServiceClient` que se conecta ao serviço móvel hospedado localmente. Remova o comentário do código que cria o `MobileServiceClient` que se conecta ao seu serviço no Azure. Salve suas alterações no arquivo.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar. Isso fará com que o aplicativo da Windows Store seja recriado com a alteração anterior antes de executar o aplicativo para se conectar ao serviço móvel hospedado remotamente no Azure. 

    ![][12]


3. Insira alguns novos todoitems e clique em **Salvar** para cada um. Clique na caixa de seleção para concluir alguns dos itens novos. Cada novo todoItem será armazenado e atualizado no banco de dados SQL configurado anteriormente para o serviço móvel no Portal de Gerenciamento do Azure. 

    ![][16]

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Você também pode examinar o banco de dados usando o Portal de Gerenciamento do Azure ou o Pesquisador de Objetos do SQL Server. As próximas duas etapas usarão o Portal de Gerenciamento do Azure para exibir as alterações no banco de dados.

4. No Portal de Gerenciamento do Azure, clique em gerenciar no banco de dados associado ao seu serviço móvel.

    ![][17]

5. No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo da Windows Store. Sua consulta será semelhante à consulta a seguir, mas use o nome do seu banco de dados, em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

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

* [Referência conceitual do tutorial do .NET dos Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.
  
<!-- Anchors. -->

[Baixar o projeto de aplicativo da Windows Store]: #download-app
[Criar um novo serviço móvel]: #create-service
[Baixar o serviço móvel localmente]: #download-the-service-locally
[Atualizar o aplicativo da Windows Store para usar o Serviço Móvel]: #update-app
[Testar o aplicativo da Windows Store no serviço hospedado localmente]: #test-locally-hosted
[Publicar o serviço móvel no Azure]: #publish-mobile-service
[Testar o aplicativo da Windows Store no serviço hospedado no Azure]: #test-azure-hosted
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-js
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
[Classe de MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

