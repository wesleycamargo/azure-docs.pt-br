<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-wp8" urlDisplayName="Introdução aos dados" pageTitle="Introdução aos dados (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como começar a usar os Serviços Móveis para utilizar dados em seu aplicativo Windows Phone." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos dados nos Serviços Móveis" authors="wesmc" solutions="" manager="" editor="" />



# Começar a trabalhar com dados em serviços móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone" class="current">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>


Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo do Windows Phone 8. Neste tutorial, você baixará um projeto do Visual Studio 2012 para um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial oferecerá suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Baixar o projeto de aplicativo do Windows Phone 8]
2. [Criar um novo serviço móvel]
3. [Baixar o serviço móvel localmente]
4. [Atualizar o aplicativo do Windows Phone para usar o serviço móvel]
5. [Testar o aplicativo do Windows Phone no serviço hospedado localmente]
6. [Publicar o serviço móvel no Azure]
7. [Testar o aplicativo do Windows Phone no serviço hospedado no Azure]

Este tutorial requer o Visual Studio 2012 e o [SDK do Windows Phone 8] em execução no Windows 8. 


<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você também precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixar o projeto GetStartedWithData</h2>

Este tutorial é baseado no [Aplicativo GetStartedWithMobileServices][Site de exemplos de código do desenvolvedor], que é um projeto de aplicativo do Windows Phone 8 no Visual Studio 2012. A interface do usuário desse aplicativo é semelhante ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória. 

1. Baixe a versão do C# do aplicativo de exemplo GetStartedWithMobileServices no [Site de exemplos de código do desenvolvedor]. 

   	![][1]

2. Execute o Visual Studio 2012 com privilégios de administrador clicando com o botão direito do mouse em Visual Studio e clicando em **Executar como administrador**.

3. No Visual Studio 2012, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection&lt;TodoItem&gt;** na memória.

4. No Visual Studio, escolha um destino de implantação para o aplicativo. Você pode implantar em um dispositivo Windows Phone ou em um dos emuladores incluídos com o SDK do Windows Phone. Neste tutorial, demonstramos a implantação em um emulador.

    ![][19]

5. Pressione a tecla **F5**. Isso irá criar, implantar e iniciar o aplicativo para depuração.

6. No aplicativo, digite texto na caixa de texto e, em seguida, clique em **Salvar** para salvar alguns itens na memória do aplicativo.

   	![][0]  

   	Observe que o texto para cada `TodoItem` é exibido abaixo do botão de atualização juntamente com uma caixa de seleção que permite que você marque o item concluído.

<h2><a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Criar um novo serviço móvel</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]



<h2><a name="download-the-service-locally"></a><span class="short-header">Baixar o serviço localmente</span>Baixar o projeto do serviço móvel e adicioná-lo à solução</h2>

1. No [Portal de Gerenciamento do Azure], clique em seu novo Serviço Móvel ou na guia do ícone de nuvem para ir para a página de visão geral.

    ![][2]

2. Clique na plataforma **Windows Phone 8**. Na seção **Introdução**, expanda **Conectar a um aplicativo Windows Phone 8 existente** e clique no botão **Baixar** para baixar um projeto inicial para o serviço móvel. 

    ![][3]

3. Também nessa seção, clique no link mostrado na captura de tela abaixo para baixar um arquivo de perfil de publicação para o serviço móvel que você acabou de baixar. 

    > [WACOM.NOTE] Salve o arquivo em um local seguro porque ele contém informações confidenciais relativas à sua conta do Azure. Você excluirá o arquivo após a publicação do serviço móvel mais tarde neste tutorial. 

    ![][5]


4. Descompacte o projeto inicial do serviço personalizado baixado. Copie as pastas que estavam no arquivo zip no mesmo diretório do **C#** onde o arquivo da solução Introdução aos dados (.sln) está localizado. Isso facilita a manutenção da sincronização de todos os pacotes pelo Gerenciador de Pacotes NuGet.

    ![][26]


5. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em sua solução para o aplicativo de Introdução a dados da Windows Store. Clique em **Adicionar** e em **Projeto Existente**.

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




<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo Windows Phone</span>Atualizar o aplicativo Windows Phone para usar o serviço móvel</h2>

Nesta seção, você irá atualizar o aplicativo Windows Phone para usar o serviço móvel como um serviço de back-end do aplicativo.


1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo Windows Phone e clique em **Gerenciar Pacotes NuGet**.

    ![][7]

2. Na caixa de diálogo Gerenciar Pacotes NuGet, pesquise por **WindowsAzure.MobileServices** na coleção de pacotes online e clique para instalar o Pacote NuGet dos Serviços Móveis do Azure. Em seguida, feche a caixa de diálogo.

    ![][8]

3. De volta no Portal de Gerenciamento do Azure, localize a etapa rotulada **Conectar seu aplicativo e armazenar dados no seu serviço**. Copie o trecho de código que cria a conexão `MobileServiceClient`.

    ![][9]

4. No Visual Studio, abra App.xaml.cs. Cole o trecho de código no início da definição da classe `App`. Além disso, adicione a seguinte instrução `using` na parte superior desse arquivo e salve o arquivo.

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. No Visual Studio, abra o MainPage.xaml.cs e adicione a seguinte instrução using na parte superior do arquivo: 

		using Microsoft.WindowsAzure.MobileServices;

6. No Visual Studio, em MainPage.xaml.cs, substitua a definição da classe `MainPage` pela definição a seguir e salve o arquivo.

    Esse código usa o SDK dos Serviços Móveis para permitir que o aplicativo armazene seus dados em uma tabela fornecida pelo serviço em vez de armazená-los localmente na memória. Os três métodos principais são `InsertTodoItem`, `RefreshTodoItems` e `UpdateCheckedTodoItem`. Esses três métodos permitem que você insira, consulte e atualize sua coleção de dados, de forma assíncrona em uma tabela no Azure. 

        public sealed partial class MainPage : PhoneApplicationPage
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
                items = await todoTable 
                    .ToCollectionAsync(); 
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
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }




<h2><a name="test-locally-hosted"></a><span class="short-header">Testar o aplicativo Windows Phone localmente</span>Testar o aplicativo Windows Phone com o serviço hospedado localmente</h2>

Nesta seção você usará o Visual Studio para testar o aplicativo e o serviço móvel localmente em sua estação de trabalho de desenvolvimento. Para testar o serviço móvel hospedado localmente no IIS Express em um dispositivo Windows Phone ou em um dos emuladores do Windows Phone, você precisa configurar o IIS Express e a estação de trabalho para permitir conexões ao endereço IP e à porta da estação de trabalho. Os emuladores e dispositivos Windows Phone se conectam como clientes de rede não local.

#### Configurar o IIS Express para permitir conexões remotas

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Testar o aplicativo no serviço móvel no IIS Express

6. No Visual Studio, abra o arquivo App.xaml.cs e comente a definição de `MobileService` recém-colada no arquivo. Adicione uma nova definição para fazer a conexão com base no endereço IP e na porta que você configurou na estação de trabalho. Em seguida, salve o arquivo. Seu código deve ser semelhante ao seguinte...

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.137.72:58203");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.preview.azure-mobile-preview.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        


7. No Visual Studio, pressione a tecla F7 ou clique em **Compilar solução** no menu Compilar para compilar o aplicativo Windows Phone e o serviço móvel. Verifique se os dois projetos são compilados sem erros na janela de saída do Visual Studio

    ![][11]

8. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar para executar o aplicativo e hospedar o serviço móvel localmente no IIS Express. 

    >[WACOM.NOTE] Verifique se você executou o Visual Studio com a opção **Executar como administrador**. Caso contrário, o IIS Express poderá não carregar suas alterações no applicationhost.config.

    ![][12]


9. Digite um novo texto de todoitem. Em seguida, clique em **Salvar**. Isso insere um novo todoItem no banco de dados criado pelo serviço móvel hospedado localmente no IIS Express. Clique na caixa de seleção de um dos itens para marcá-lo como concluído.

    ![][15]

10. No Visual Studio, pare a depuração do aplicativo. Você pode exibir as alterações no banco de dados criado para o serviço de back-end abrindo o Gerenciador de Servidores e expandindo as Conexões de Dados. Clique com o botão direito do mouse na tabela TodoItems em **MS_TableConnectionString** e clique em **Mostrar Dados da Tabela**

    ![][14]

11. Ao concluir o teste com o serviço móvel hospedado localmente, exclua a regra de Firewall do Windows criada que abriu a porta em sua estação de trabalho.



<h2><a name="publish-mobile-service"></a><span class="short-header">Publicar o serviço móvel no Azure</span>Publicar o serviço móvel no Azure</h2>


[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<h2><a name="test-azure-hosted"></a><span class="short-header">Testar o serviço móvel no Azure</span>Testar o serviço móvel publicado no Azure</h2>

1. No Visual Studio, abra App.xaml.cs.  Comente o código que cria o `MobileServiceClient` que se conecta ao serviço móvel hospedado localmente. Remova o comentário do código que cria o `MobileServiceClient` que se conecta ao seu serviço no Azure. Salve suas alterações no arquivo.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.137.72:58203");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar. Isso fará com que o aplicativo seja recriado com a alteração anterior antes de executar o aplicativo para se conectar ao serviço móvel hospedado remotamente no Azure. 

    ![][12]


3. Insira alguns novos todoitems e clique em **Salvar** para cada um. Clique na caixa de seleção para concluir alguns dos itens novos. Cada novo todoItem será armazenado e atualizado no banco de dados SQL configurado anteriormente para o serviço móvel no Portal de Gerenciamento do Azure. 

    ![][16]

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Você também pode examinar o banco de dados usando o Portal de Gerenciamento do Azure ou o Pesquisador de Objetos do SQL Server. As próximas duas etapas usarão o Portal de Gerenciamento do Azure para exibir as alterações no banco de dados.


4. No Portal de Gerenciamento do Azure, clique em gerenciar no banco de dados associado ao seu serviço móvel.

    ![][17]

5. No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. Sua consulta será semelhante à consulta a seguir, mas use o nome do seu banco de dados, em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas de como habilitar um aplicativo Windows Phone 8 para trabalhar com dados nos Serviços Móveis que são criados usando o tempo de execução do .NET. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

* [Validar e modificar os dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Aprenda a usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo.

<!--
* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
-->

* [Referência conceitual do tutorial do .NET dos Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
  
<!-- Anchors. -->

[Baixar o projeto de aplicativo do Windows Phone 8]: #download-app
[Criar um novo serviço móvel]: #create-service
[Baixar o serviço móvel localmente]: #download-the-service-locally
[Atualizar o aplicativo do Windows Phone para usar o Serviço Móvel]: #update-app
[Testar o aplicativo do Windows Phone no serviço hospedado localmente]: #test-locally-hosted
[Publicar o serviço móvel no Azure]: #publish-mobile-service
[Testar o aplicativo do Windows Phone no serviço hospedado no Azure]: #test-azure-hosted
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
[20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
[26]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png



<!-- URLs. -->
[Validar e modificar os dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8

[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?linkid=271146
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Classe de MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Como adicionar uma nova regra de porta do Firewall do Windows]:  http://go.microsoft.com/fwlink/?LinkId=392240

