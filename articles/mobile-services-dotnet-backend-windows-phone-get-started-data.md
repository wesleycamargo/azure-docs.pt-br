<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Phone app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo do Windows Phone (Silverlight). Neste tutorial, você baixará um projeto do Visual Studio para um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial oferecerá suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo do Windows Phone 8][Baixar o projeto de aplicativo do Windows Phone 8]
2.  [Criar um novo serviço móvel][Criar um novo serviço móvel]
3.  [Baixar o serviço móvel localmente][Baixar o serviço móvel localmente]
4.  [Atualizar o aplicativo do Windows Phone para usar o serviço móvel][Atualizar o aplicativo do Windows Phone para usar o serviço móvel]
5.  [Testar o aplicativo do Windows Phone no serviço hospedado localmente][Testar o aplicativo do Windows Phone no serviço hospedado localmente]
6.  [Publicar o serviço móvel no Azure][Publicar o serviço móvel no Azure]
7.  [Testar o aplicativo do Windows Phone no serviço hospedado no Azure][Testar o aplicativo do Windows Phone no serviço hospedado no Azure]

Este tutorial exige o seguinte:

-   Visual Studio 2013 e o [SDK do Windows Phone 8][SDK do Windows Phone 8] em execução no Windows 8. Para concluir este tutorial para criar um aplicativo do Windows Phone 8.1, você deve usar a atualização 2 do Visual Studio 2013 ou uma versão posterior.
-   Uma conta do Microsoft Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
    </p>
    </div>

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][aplicativo GetStartedWithMobileServices], que é um aplicativo do projeto do Windows Phone Silverlight 8. A interface do usuário desse aplicativo é semelhante a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1.  Baixe a versão C# do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor][aplicativo GetStartedWithMobileServices].

    ![][0]

    > [WACOM.NOTE]Para criar um aplicativo do Windows Phone Silverlight 8.1, simplesmente altere o SO de destino no projeto do aplicativo do Windows Phone Silverlight 8 baixado para o Windows Phone 8.1. Para criar um aplicativo do Windows Phone Store, baixe a [versão do aplicativo do Windows Phone Store][versão do aplicativo do Windows Phone Store] do projeto do aplicativo de exemplo GetStartedWithData.

2.  Execute o Visual Studio com privilégios de administrador clicando com o botão direito do mouse em Visual Studio e clicando em **Executar como administrador**.

3.  No Visual Studio, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection\<TodoItem\>** na memória.

4.  No Visual Studio, escolha um destino de implantação para o aplicativo. Você pode implantar em um dispositivo Windows Phone ou em um dos emuladores incluídos com o SDK do Windows Phone. Neste tutorial, demonstramos a implantação em um emulador.

    ![][1]

5.  Pressione a tecla **F5**. Isso irá criar, implantar e iniciar o aplicativo para depuração.

6.  No aplicativo, digite texto na caixa de texto e, em seguida, clique em **Salvar** para salvar alguns itens na memória do aplicativo.

    ![][2]

    Observe que o texto para cada `TodoItem` é exibido abaixo do botão de atualização juntamente com uma caixa de seleção que permite que você marque o item concluído.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a><span class="short-header">Baixar o serviço localmente</span>Baixar o projeto do serviço móvel e adicioná-lo à solução

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em seu novo Serviço Móvel ou na guia do ícone de nuvem para acessar a página de visão geral.

    ![][3]

2.  Clique na plataforma **Windows Phone 8**. Na seção **Introdução**, expanda **Conectar a um aplicativo Windows Phone 8 existente** e clique no botão **Baixar** para baixar um projeto inicial para o serviço móvel.

    ![][4]

3.  Também nessa seção, clique no link mostrado na captura de tela abaixo para baixar um arquivo de perfil de publicação para o serviço móvel que você acabou de baixar.

    > [WACOM.NOTE] Salve o arquivo em um local seguro, porque ele contém informações confidenciais relativas à sua conta do Azure. Você excluirá o arquivo após a publicação do serviço móvel mais tarde neste tutorial.

    ![][5]

4.  Descompacte o projeto inicial do serviço personalizado baixado. Copie as pastas que estavam no arquivo zip no mesmo diretório do **C#** onde o arquivo da solução Introdução aos dados (.sln) está localizado. Isso facilita a manutenção da sincronização de todos os pacotes pelo Gerenciador de Pacotes NuGet.

    ![][6]

5.  No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em sua solução para o aplicativo de Introdução a dados da Windows Store. Clique em **Adicionar** e em **Projeto Existente**.

    ![][7]

6.  Na caixa de diálogo Adicionar Projeto Existente, navegue para a pasta do projeto do serviço móvel que você moveu para o diretório do **C#**. Selecione o arquivo do projeto C# (.csproj) no subdiretório do serviço. Clique em **Abrir** para adicionar o projeto à sua solução.

    ![][8]

7.  No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço que você acabou de adicionar e clique em **Compilar** para verificar se ele é compilado sem erros. Durante a compilação, o Gerenciador de Pacotes NuGet talvez precise restaurar alguns pacotes NuGet referenciados no projeto.

    ![][9]

8.  Clique com o botão direito do mouse no projeto do serviço novamente. Desta vez, clique em **Iniciar nova instância** na caixa de contexto **Depurar**.

    ![][10]

    O Visual Studio abre a página da web padrão do serviço. Você pode clicar em **Experimentar agora** para testar métodos no serviço móvel da página da web padrão.

    ![][11]

    Por padrão, o Visual Studio hospeda o serviço móvel localmente no IIS Express. Você pode ver isso, clicando com o botão direito do mouse no ícone de bandeja do IIS Express na barra de tarefas.

    ![][12]

## <a name="update-app"></a>Atualizar o aplicativo do Windows Phone para usar o serviço móvel

Nesta seção, você irá atualizar o aplicativo Windows Phone para usar o serviço móvel como um serviço de back-end do aplicativo.

1.  No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo Windows Phone e clique em **Gerenciar Pacotes NuGet**.

    ![][13]

2.  Na caixa de diálogo Gerenciar Pacotes NuGet, pesquise por **WindowsAzure.MobileServices** na coleção de pacotes online e clique para instalar o Pacote NuGet dos Serviços Móveis do Azure. Em seguida, feche a caixa de diálogo.

    ![][14]

3.  De volta no Portal de Gerenciamento do Azure, localize a etapa rotulada **Conectar seu aplicativo e armazenar dados no seu serviço**. Copie o trecho de código que cria a conexão `MobileServiceClient`.

    ![][15]

4.  No Visual Studio, abra App.xaml.cs. Cole o trecho de código no início da definição da classe `App`. Além disso, adicione a seguinte instrução `using` na parte superior desse arquivo e salve o arquivo.

        using Microsoft.WindowsAzure.MobileServices;

    ![][16]

5.  No Visual Studio, abra o MainPage.xaml.cs e adicione a seguinte instrução using na parte superior do arquivo:

        using Microsoft.WindowsAzure.MobileServices;

6.  No Visual Studio, em MainPage.xaml.cs, substitua a definição da classe `MainPage` pela definição a seguir e salve o arquivo.

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

## <a name="test-locally-hosted"></a>Testar o aplicativo do Windows Phone no serviço hospedado localmente

</h2>
Nesta seção você usará o Visual Studio para testar o aplicativo e o serviço móvel localmente em sua estação de trabalho de desenvolvimento. Para testar o serviço móvel hospedado localmente no IIS Express em um dispositivo Windows Phone ou em um dos emuladores do Windows Phone, você precisa configurar o IIS Express e a estação de trabalho para permitir conexões ao endereço IP e à porta da estação de trabalho. Os emuladores e dispositivos Windows Phone se conectam como clientes de rede não local.

#### Configurar o IIS Express para permitir conexões remotas

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Testar o aplicativo no serviço móvel no IIS Express

1.  No Visual Studio, abra o arquivo App.xaml.cs e comente a definição de `MobileService` recém-colada no arquivo. Adicione uma nova definição para fazer a conexão com base no endereço IP e na porta que você configurou na estação de trabalho. Em seguida, salve o arquivo. Seu código deve ser semelhante ao seguinte...

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        

2.  No Visual Studio, pressione a tecla F7 ou clique em **Compilar solução** no menu Compilar para compilar o aplicativo Windows Phone e o serviço móvel. Verifique se os dois projetos são compilados sem erros na janela de saída do Visual Studio

    ![][17]

3.  No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar para executar o aplicativo e hospedar o serviço móvel localmente no IIS Express.

    > [WACOM.NOTE] Verifique se você executou o Visual Studio com a opção **Executar como administrador**. Caso contrário, o IIS Express poderá não carregar suas alterações no applicationhost.config.

    ![][18]

4.  Digite um novo texto de todoitem. Em seguida, clique em **Salvar**. Isso insere um novo todoItem no banco de dados criado pelo serviço móvel hospedado localmente no IIS Express. Clique na caixa de seleção de um dos itens para marcá-lo como concluído.

    ![][19]

5.  No Visual Studio, pare a depuração do aplicativo. Você pode exibir as alterações no banco de dados criado para o serviço de back-end abrindo o Gerenciador de Servidores e expandindo as Conexões de Dados. Clique com o botão direito do mpuse na tabela TodoItems em **MS\_TableConnectionString** e clique em **Mostrar Dados da Tabela**

    ![][20]

6.  Ao concluir o teste com o serviço móvel hospedado localmente, exclua a regra de Firewall do Windows criada que abriu a porta em sua estação de trabalho.

## <a name="publish-mobile-service"></a>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Testar o serviço móvel publicado no Azure

1.  No Visual Studio, abra App.xaml.cs. Comente o código que cria o `MobileServiceClient` que se conecta ao serviço móvel hospedado localmente. Remova o comentário do código que cria o `MobileServiceClient`que se conecta ao seu serviço no Azure. Salve suas alterações no arquivo.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2.  No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu Depurar. Isso fará com que o aplicativo seja recriado com a alteração anterior antes de executar o aplicativo para se conectar ao serviço móvel hospedado remotamente no Azure.

    ![][18]

3.  Insira alguns novos todoitems e clique em **Salvar** para cada um. Clique na caixa de seleção para concluir alguns dos itens novos. Cada novo todoItem será armazenado e atualizado no banco de dados SQL configurado anteriormente para o serviço móvel no Portal de Gerenciamento do Azure.

    ![][21]

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Você também pode examinar o banco de dados usando o Portal de Gerenciamento do Azure ou o Pesquisador de Objetos do SQL Server. As próximas duas etapas usarão o Portal de Gerenciamento do Azure para exibir as alterações no banco de dados.

4.  No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![][22]

5.  No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. Sua consulta será semelhante à consulta a seguir, mas usará seu nome de banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][23]

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas de como habilitar um aplicativo Windows Phone 8 para trabalhar com dados nos Serviços Móveis que são criados usando o tempo de execução do .NET. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo.


-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com o .NET.

 
 


  [Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
  [Baixar o projeto de aplicativo do Windows Phone 8]: #download-app
  [Criar um novo serviço móvel]: #create-service
  [Baixar o serviço móvel localmente]: #download-the-service-locally
  [Atualizar o aplicativo do Windows Phone para usar o serviço móvel]: #update-app
  [Testar o aplicativo do Windows Phone no serviço hospedado localmente]: #test-locally-hosted
  [Publicar o serviço móvel no Azure]: #publish-mobile-service
  [Testar o aplicativo do Windows Phone no serviço hospedado no Azure]: #test-azure-hosted
  [SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [aplicativo GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?linkid=271146
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
  [versão do aplicativo do Windows Phone Store]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
  [4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
  [5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
  [6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png
  [7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
  [8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
  [9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
  [10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
  [11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
  [12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
  [13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
  [14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
  [15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
  [16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
  [17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
  [18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
  [19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
  [20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
  [21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
  [22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
  [23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
