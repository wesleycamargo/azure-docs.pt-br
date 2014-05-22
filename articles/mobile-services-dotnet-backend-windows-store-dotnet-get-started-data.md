<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-dotnet-vs2013" urlDisplayName="Introdução aos dados" pageTitle="Introdução aos dados (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como começar a usar os Serviços Móveis para utilizar os dados em seu aplicativo da Windows Store" metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos dados nos Serviços Móveis" authors="wesmc" solutions="" manager="" editor="" />



# Começar a trabalhar com dados em serviços móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# da Windows Store" class="current">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
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


<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][Site de exemplos de código do desenvolvedor], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário desse aplicativo é semelhante ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória. 

1. Baixe a versão do C# do aplicativo de exemplo GetStartedWithMobileServices no [Site de exemplos de código do desenvolvedor]. 

   	![][1]

2. Execute o Visual Studio 2013 com privilégios de administrador clicando com o botão direito do mouse em Visual Studio e clicando em **Executar como administrador**.

3. No Visual Studio 2013, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection&lt;TodoItem&gt;** na memória.

4. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

5. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

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


4. Descompacte o projeto inicial do serviço personalizado baixado. Copie as pastas que estavam no arquivo zip no mesmo diretório do **C#** onde o arquivo da solução Introdução aos dados (.sln) está localizado. Isso facilita a manutenção da sincronização de todos os pacotes pelo Gerenciador de Pacotes NuGet.

    ![][26]

5. Em seguida, no Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em sua solução para o aplicativo de Introdução a dados da Windows Store. Clique em **Adicionar** e em **Projeto Existente**.

    ![][4]

6. Na caixa de diálogo Adicionar Projeto Existente, navegue para a pasta do projeto do serviço móvel que você moveu para o diretório do **C#**. Selecione o arquivo do projeto C# (.csproj) no subdiretório do serviço. Clique em **Abrir** para adicionar o projeto à sua solução.

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

2. Na caixa de diálogo Gerenciar Pacotes NuGet, pesquise por **WindowsAzure.MobileServices** na coleção de pacotes online e clique para instalar o Pacote NuGet dos Serviços Móveis do Azure. Em seguida, feche a caixa de diálogo.

    ![][8]

3. De volta no Portal de Gerenciamento do Azure, localize a etapa rotulada **Conectar seu aplicativo e armazenar dados no seu serviço**. Verifique se a linguagem **C#** está selecionada. Copie o trecho de código que cria a conexão `MobileServiceClient`.

    ![][9]

4. No Visual Studio, abra App.xaml.cs. Cole o trecho de código no início da definição da classe `App`. Além disso, adicione a seguinte instrução `using` na parte superior desse arquivo e salve o arquivo.

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. No Visual Studio, abra MainPage.xaml.cs e adicione a seguinte instrução using: 

		using Microsoft.WindowsAzure.MobileServices;

6. No Visual Studio, em MainPage.xaml.cs, substitua a definição da classe `MainPage` pela definição a seguir e salve o arquivo. 

    Esse código usa o SDK dos Serviços Móveis para permitir que o aplicativo armazene seus dados em uma tabela fornecida pelo serviço em vez de localmente na memória. Os três métodos principais são `InsertTodoItem`, `RefreshTodoItems` e `UpdateCheckedTodoItem`. Esses três métodos permitem que você insira, consulte e atualize sua coleção de dados, de forma assíncrona em uma tabela no Azure.

        public sealed partial class MainPage : Page
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }




<h2><a name="test-locally-hosted"></a><span class="short-header">Testar o aplicativo da Windows Store localmente</span>Testar o aplicativo da Windows Store com o serviço hospedado localmente</h2>

Nesta seção você usará o Visual Studio para hospedar o aplicativo e o serviço móvel localmente em sua estação de trabalho de desenvolvimento no IIS Express. Em seguida, você testará o aplicativo e o serviço de back-end.


1. No Visual Studio, pressione a tecla F7 ou clique em **Compilar solução** no menu Compilar para compilar o aplicativo da Windows Store e o serviço móvel. Verifique se os dois projetos são compilados sem erros na janela de saída do Visual Studio

    ![][11]

2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar para executar o aplicativo e hospedar o serviço móvel localmente no IIS Express. 


3. Digite um novo texto de todoitem. Em seguida, clique em **Salvar**. Isso insere um novo todoItem no banco de dados criado pelo serviço móvel hospedado localmente no IIS Express. 

    ![][13]

4. Clique na caixa de seleção de um dos itens para marcá-lo como concluído.

    ![][15]

5. No Visual Studio, você pode exibir as alterações no banco de dados criado para o serviço de back-end abrindo o Gerenciador de Servidores e expandindo as Conexões de Dados. Clique com o botão direito do mouse na tabela TodoItems em **MS_TableConnectionString** e clique em **Mostrar Dados da Tabela**

    ![][14]



<h2><a name="publish-mobile-service"></a><span class="short-header">Publicar o serviço móvel no Azure</span>Publicar o serviço móvel no Azure</h2>


[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<h2><a name="test-azure-hosted"></a><span class="short-header">Testar o serviço móvel no Azure</span>Testar o serviço móvel publicado no Azure</h2>

1. No Visual Studio, abra App.xaml.cs.  Comente o código que cria o `MobileServiceClient` que se conecta ao serviço móvel hospedado localmente. Remova o comentário do código que cria o `MobileServiceClient` que se conecta ao seu serviço no Azure. Salve suas alterações no arquivo.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar. Isso fará com que o aplicativo da Windows Store seja recriado com a alteração anterior antes de executar o aplicativo para se conectar ao serviço móvel hospedado remotamente no Azure. 


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
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
  
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Classe de MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

