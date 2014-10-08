<properties linkid="develop-mobile-tutorials-get-started-offline-data-wp8" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Introdução à sincronização de dados offline nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# da Windows Store">C# da Windows Store</a>
<a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone" class="current">Windows Phone</a>
<a href="/en-us/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/en-us/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/en-us/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Este tópico mostra como usar os recursos offline dos Serviços Móveis do Azure. Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Os recursos offline permitem que você sincronize suas alterações locais com o serviço móvel quando estiver online novamente.

Neste tutorial, você irá atualizar o aplicativo do tutorial [Introdução aos Serviços Móveis][] ou [Introdução aos Dados][] para oferecer suporte aos recursos offline dos Serviços Móveis do Azure. Você irá adicionar dados em um cenário desconectado offline, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE] Este tutorial é destinado a ajudar você a entender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo Windows Phone. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis][].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Atualizar o aplicativo para dar suporte aos recursos offline][]
2.  [Testar o aplicativo em um cenário offline][]
3.  [Atualizar o aplicativo para reconectar seu serviço móvel][]
4.  [Testar o aplicativo conectado ao Serviço Móvel][]

Este tutorial exige o seguinte:

-   Visual Studio 2012
-   [SDK do Windows Phone 8][]
-   Conclusão do tutorial [Introdução aos Serviços Móveis][] ou [Introdução aos Dados][].
-   [Azure Mobile Services SDK versão 1.3.0-alpha4 (ou posterior)][]
-   [Azure Mobile Services SQLite Store versão 1.0.0-alpha4 (ou posterior)][]
-   [SQLite for Windows Phone 8][]

> [WACOM.NOTE] Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, você deve inicializar `MobileServiceClient.SyncContext` para um armazenamento local. Faça referência à sua tabela através da interface `IMobileServiceSyncTable`.

Esta seção usa o SQLite como o armazenamento local para os recursos offline.

> [WACOM.NOTE] É possível ignorar esta seção e apenas baixar uma versão do projeto Introdução que já tenha suporte offline. Para baixar um projeto com suporte offline habilitado, consulte [Introdução à Amostra Offline para o Windows Phone][].

1.  Instale projetos do SQLite for Windows Phone 8. É possível instalá-lo a partir deste link, [SQLite for Windows Phone 8][].

    > [WACOM.NOTE] Se estiver usando o Internet Explorer, clicar no link para instalar o SQLite pode avisá-lo para baixar o .vsix como um arquivo .zip. Salve o arquivo em um local no seu disco rígido com a extensão .vsix em vez de .zip. O duplo clique no arquivo .vsix no Windows Explorer para executar a instalação.

2.  No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis][] ou [Introdução aos Dados][]. No Gerenciador de Soluções, clique com o botão direito em **Referências** no projeto e inclua uma referência ao **SQLite for Windows Phone** em **Windows Phone**\>**Extensões**.

    ![][]

3.  O Tempo de Execução do SQLite requer que altere a arquitetura do processador do projeto que está sendo criado para **x86**, **x64** ou **ARM**. Não há suporte para **Qualquer CPU**. Altere a arquitetura do processador para uma das configurações com suporte que deseja testar.

    ![][1]

4.  No Gerenciador de Soluções para o Visual Studio, clique com o botão direito em seu projeto de aplicativo do cliente e clique em **Gerenciar Pacotes NuGet** para executar o Gerenciador de Pacotes NuGet. Procurar por **SQLiteStore** para instalar o pacote **WindowsAzure.MobileServices.SQLiteStore**.

    ![][2]

5.  No Gerenciador de Soluções para o Visual Studio, abra o arquivo MainPage.xaml.cs. Adicione o seguinte usando as instruções na parte superior do arquivo.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6.  No arquivo Mainpage.xaml.cs, substitua a declaração da `todoTable` por uma declaração do tipo `IMobileServicesSyncTable` que é inicializada chamando `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7.  No arquivo MainPage.xaml.cs, atualize a classe `TodoItem` para que a classe inclua o sistema de propriedade **Versão** da seguinte maneira.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }

8.  No arquivo MainPage.xaml.cs, atualize o manipulador de eventos `OnNavigatedTo` para que ele seja um método `async` e inicialize o contexto de sincronização do cliente com um armazenamento do SQLite. O armazenamento do SQLite é criado com uma tabela que coincide com o esquema da tabela do serviço móvel, mas ele deve conter o sistema de propriedade **versão** adicionado na etapa anterior.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9.  No Gerenciador de Soluções para o Visual Studio, abra o arquivo MainPage.xaml. Localize a definição do botão para o botão **Atualizar**. Substitua-a pela definição de painel de pilha a seguir.

    Este código inclui dois controles de botão com manipuladores de eventos de clique para operações **Push** e **Pull**. Os botões são horizontais em relação a onde o botão de atualizar está. Salve o arquivo.

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    Além disso, altere o texto para os blocos de texto a fim de que correspondam à captura de tela a seguir.

    ![][3]

10. Em MainPage.xaml.cs, inclua os manipuladores de eventos de clique de botão para os botões **Push** e **Pull** salve o arquivo.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync();
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. Não execute o aplicativo ainda. Pressione a tecla **F7** para recompilar o projeto. Verifique se não ocorreu nenhum erro de compilação.

## <a name="test-offline-app"></a>Testar o aplicativo em um cenário offline

Nesta seção, você interrompe a conexão do aplicativo com o serviço móvel para simular um cenário offline. Em seguida, você adiciona alguns itens de dados que serão mantidos no armazenamento local.

Observe que, nesta seção, o aplicativo não deve estar conectado a nenhum serviço móvel. Portanto, os botões **Enviar por Push** e **Efetuar Pull** gerarão exceções se você testá-los. Na próxima seção, você conectará esse aplicativo cliente ao serviço móvel novamente para testar as operações **Push** e **Pull** para sincronizar o armazenamento com o banco de dados do serviço móvel.

1.  No Gerenciador de Soluções para o Visual Studio, abra App.xaml.cs. Altere a inicialização do **MobileServiceClient** para um endereço inválido substituindo "**azure-mobile.net**" por "**azure-mobile.xxx**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2.  No Visual Studio, pressione **F5** para criar e executar o aplicativo. Insira um novo item de tarefas pendentes e clique em **Salvar**. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao serviço móvel oferecendo suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

    ![][4]

3.  Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu serviço móvel

Nesta seção você reconecta o aplicativo ao serviço móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o serviço móvel.

1.  No Gerenciador de Soluções para o Visual Studio, abra App.xaml.cs. Altere a inicialização do **MobileServiceClient** novamente para o endereço correto substituindo "**azure-mobile.xxx**" por "**azure-mobile.net**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

## <a name="test-online-app"></a>Testar o aplicativo conectado ao serviço móvel

Nesta seção você testará as operações de push e pull para sincronizar o repositório local com o banco de dados do serviço móvel.

1.  No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo. Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao serviço móvel. Isso ocorre porque esse aplicativo sempre funciona com o `IMobileServiceSyncTable` que está apontado para o repositório local.

    ![][4]

2.  Faça logon no Portal de Gerenciamento do Microsoft Azure e observe o banco de dados para seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel. Se estiver usando o back-end do .NET para o serviço móvel, você poderá clicar no botão **Gerenciar** do banco de dados no SQL Azure Extension para executar uma consulta na tabela.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

    ![][5]

3.  No aplicativo, pressione o botão **Enviar por Push**. Isso faz com que o aplicativo chame `MobileServiceClient.SyncContext.PushAsync` e `RefreshTodoItems` para atualizar o aplicativo com os itens do armazenamento local. Essa operação push faz com que o banco de dados do serviço móvel receba os dados do repositório. No entanto, o repositório local não recebe os itens do banco de dados do serviço móvel.

    Uma operação de push é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre tabelas.

    ![][6]

4.  No aplicativo, alguns novos itens no armazenamento local.

    ![][7]

5.  Desta vez, pressione o botão **Executar Pull** no aplicativo. O aplicativo apenas chamará `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`. Observe que é efetuado pull de todos os dados do banco de dados do serviço móvel para o repositório local, conforme mostrado no aplicativo. No entanto, observe também que todos os dados do repositório local ainda foram enviados por push ao banco de dados do serviço móvel. Isso ocorre porque um **pull sempre executa um push primeiro**.

    ![][8]

    ![][9]

## Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

-   Para executar push das mudanças no servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados de alguma forma localmente (por meio de operações CRUD) serão enviados ao servidor.

-   Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Uma pull sempre envia um push primeiro.

    Também há sobrecargas de **PullAsync()** que permitem que uma consulta seja especificada. Observe que, na versão de visualização do suporte offline para os Serviços Móveis, **PullAsync** lerá todas as linhas na tabela correspondente (ou consulta)--ele não tenta ler apenas as linhas mais recentes do que a última sincronização, por exemplo. Se as linhas já existirem na tabela de sincronização local, elas permanecerão inalteradas.

-   Para baixar um projeto com suporte offline habilitado, consulte [Introdução à Amostra Offline para o Windows Phone][].

## Próximas etapas

-   [Tratando conflitos com o suporte offline para Serviços Móveis][]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [C# da Windows Store]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "C# da Windows Store"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /en-us/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /en-us/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-phone-get-started/
  [Introdução aos Dados]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
  [Testar o aplicativo em um cenário offline]: #test-offline-app
  [Atualizar o aplicativo para reconectar seu serviço móvel]: #update-online-app
  [Testar o aplicativo conectado ao Serviço Móvel]: #test-online-app
  [SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Azure Mobile Services SDK versão 1.3.0-alpha4 (ou posterior)]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha4
  [Azure Mobile Services SQLite Store versão 1.0.0-alpha4 (ou posterior)]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha4
  [SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [Introdução à Amostra Offline para o Windows Phone]: http://go.microsoft.com/fwlink/?LinkId=397952
  []: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
  [1]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
  [2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
  [3]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png
  [4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
  [5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
  [6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
  [7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
  [8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
  [9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
  [Tratando conflitos com o suporte offline para Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data/
