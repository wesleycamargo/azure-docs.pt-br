<properties urlDisplayName="Using Offline Data" pageTitle="Usando dados offline nos Serviços Móveis (Windows Store) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como usar serviços móveis do Azure para cache e sincronização de dados offline no seu aplicativo da Windows Store" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Usando sincronização de dados offline em Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar suporte offline para um aplicativo Universal da Windows Store usando Serviços Móveis do Azure. Suporte offline permitirá que você interaja com um banco de dados local quando seu aplicativo estiver em um cenário offline. Quando seu aplicativo está online com o banco de dados back-end, você pode sincronizar as alterações locais usando recursos offline. 
</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir vídeo</span></a> <span class="time">14:36</span></div>
</div>


Neste tutorial, você atualizará o projeto de aplicativo Universal de [Introdução aos Serviços Móveis] para dar suporte a recursos offline dos Serviços Móveis do Azure. Você adicionará dados em um cenário offline desconectado, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.


>[WACOM.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Se esta for sua primeira experiência com Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].
>
>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>. 
>
>O tutorial mais antigo do Windows Phone 8 para Visual Studio 2012 ainda está disponível aqui, [Tutorial do Windows Phone 8 para o Visual Studio 2012].


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para dar suporte aos recursos offline]
2. [Testar o aplicativo em um cenário offline] 
3. [Atualizar o aplicativo para reconectar seu serviço móvel]
4. [Testar o aplicativo conectado ao Serviço Móvel]

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão de [Introdução aos serviços móveis].
* [SDK dos Serviços Móveis do Azure versão 1.3.0-beta2 (ou posterior)][Nuget de SDK para Serviços Móveis]
* [SQLite Store para Serviços Móveis do Azure versão 1.0.0-beta2 (ou posterior))][Nuget de armazenamento de SQLite]
* SQLite para Windows 8.1

>[AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>. 

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, você deve inicializar um `MobileServiceClient.SyncContext` para um armazenamento local. Faça referência à sua tabela através da interface `IMobileServiceSyncTable`. Neste tutorial, usamos SQLite para o armazenamento local.

>[AZURE.NOTE] Você pode ignorar esta seção e obter apenas o projeto de exemplo que já tem o suporte offline do repositório de amostras Github para os Serviços Móveis. O projeto de exemplo com o suporte offline habilitado está localizado aqui, [Exemplo offline da TodoList].


1. Instale o tempo de execução do SQLite para Windows 8.1 e Windows Phone 8.1. 

    * **Tempo de execução do Windows 8.1:** Instale o tempo de execução do SQLite para Windows 8.1 desse link, [SQLite para Windows 8.1].
    * **Windows Phone 8,1:** Instale o tempo de execução do SQLite para Windows Phone 8.1 desse link, [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE] Se você estiver usando o Internet Explorer, clicando no link para instalar o SQLite pode ser solicitado que você baixe o arquivo .vsix como um arquivo .zip. Salve o arquivo em um local no seu disco rígido com a extensão .vsix em vez de .zip. O duplo clique no arquivo .vsix no Windows Explorer para executar a instalação.

2. No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis]. No Gerenciador de Soluções, clique com botão direito do mouse em **Referências** para os projetos de plataforma de tempo de execução do Windows 8.1 e Windows Phone 8.1 e adicione uma referência ao SQLite, localizado na seção **Extensões**. 

    ![][1]
    </br>**Tempo de execução do Windows 8.1**

    ![][11]
    </br>**Windows Phone 8,1**

3.  O Tempo de Execução do SQLite exige que você altere a arquitetura do processador do projeto que está sendo criado para **x86**, **x64**, ou **ARM**. Não há suporte para **Qualquer CPU** . No Gerenciador de Soluções do Visual Studio, clique em Solução na parte superior e altere a caixa suspensa de arquitetura do processador para uma das configurações com suporte que você deseja testar.

    ![][13]

4. Instale o pacote NuGet  **WindowsAzure.MobileServices.SQLiteStore** para o tempo de execução do Windows 8.1 e projetos do Windows Phone 8.1.

    * **Windows 8.1:** No Gerenciador de Soluções, clique com botão direito do mouse no projeto Windows 8.1 e clique em **Gerenciar Pacotes NuGet** para executar o Gerenciador de pacotes do NuGet. Procure por **SQLiteStore** para instalar o pacote WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8,1:** Clique com botão direito do mouse no projeto do Windows Phone 8.1 e clique em **Gerenciar Pacotes NuGet** para executar o Gerenciador de pacotes do NuGet. Procure por **SQLiteStore** para instalar o pacote WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Se a instalação criar uma referência a uma versão anterior do SQLite, exclua essa referência duplicada. 

    ![][2]

5. No Gerenciador de Soluções do Visual Studio, no projeto compartilhado, abra o arquivo MainPage.cs. Adicione o seguinte usando as instruções na parte superior do arquivo.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. No arquivo compartilhado Mainpage.cs, substitua a declaração da `todoTable` por uma declaração do tipo `IMobileServicesSyncTable` que é inicializada chamando `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. No arquivo compartilhado, MainPage.cs, atualize o manipulador de eventos `OnNavigatedTo` para que ele inicialize o contexto de sincronização do cliente com um armazenamento do SQLite. O armazenamento do SQLite é criado com uma tabela que coincide com o esquema da tabela do serviço móvel, mas ele deve conter a propriedade do sistema**Version** que você adicionará na próxima etapa.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. No Gerenciador de Soluções do Visual Studio, no projeto compartilhado, expanda **DataModel** e abra TodoItem.cs. Adicione uma instrução `usando` para o namespace MobileServices. Isso é necessário para resolver o atributo de versão para a propriedade de versão do sistema.

        using Microsoft.WindowsAzure.MobileServices;

      Depois atualize a classe `TodoItem` para que a classe inclua a propriedade do sistema **Version** da seguinte maneira. O esquema da tabela deve incluir a propriedade **Version** do sistema como mostrado aqui para dar suporte aos recursos offline.

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




9. Em seguida, atualize a interface do usuário para os projetos Windows 8.1 e Windows Phone 8.1 inclua botões que darão suporte as operações de sincronização offline entre o banco de dados offline local e o banco de dados de Serviço Móvel no Azure. 

    * **Windows 8.1:** No Gerenciador de Soluções do Visual Studio, no projeto do Windows 8.1, abra MainPage. XAML. Localize o botão denominado **ButtonRefresh**. Substitua esse elemento de botão pelo painel de pilha de botões abaixo. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8,1:** No Gerenciador de Soluções do Visual Studio, no projeto Windows 8.1, abra MainPage. XAML. Localize o botão denominado **ButtonRefresh**. Substitua esse elemento de botão pelo painel de pilha de botões abaixo. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. No arquivo compartilhado MainPage.cs, adicione manipuladores de eventos de clique aos botões **Push** e **Pull**. Em seguida, salve o arquivo. 
        
    Observe que `MobileServicePushFailedException` pode ocorrer devido a um esforço e uma operação de recepção. Pode ocorrer para um pull porque a operação de pull executa internamente um envio por push para garantir que todas as tabelas, juntamente com quaisquer relacionamentos, estejam sincronizadas. O próximo tutorial, [Tratando conflitos com o suporte offline para Serviços Móveis], mostra como tratar essas exceções relacionadas de sincronização.

    Para dar suporte à sincronização dos registros excluídos com a sincronização de dados offline, você deve habilitar [Exclusão Reversível](/pt-br/documentation/articles/mobile-services-using-soft-delete/). Caso contrário, você precisa remover registros no armazenamento local, manualmente ou chamar `IMobileServiceSyncTable::PurgeAsync()` para limpar o armazenamento local.

    Nesse exemplo, passamos uma consulta para a chamada do método `PullAsync` para dar suporte à sincronização incremental. Isso é útil em casos em que você não quer a despesa de recepção de toda a tabela durante a sincronização. Neste cenário, não estamos preocupados com alterações no texto todoitems, queremos extrair apenas itens concluídos para marcá-los fora da todolist.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Crie a solução e verifique se que não ocorreu nenhum erro de compilação em qualquer um dos projetos.


## <a name="test-offline-app"></a>Testar o aplicativo em um cenário offline

Nesta seção, você interrompe a conexão do aplicativo com o serviço móvel para simular um cenário offline. Em seguida, você adiciona alguns itens de dados que serão mantidos no repositório local.

Observe que, nesta seção, o aplicativo não deverá estar conectado a nenhum serviço móvel. Portanto, os botões **Enviar por Push** e **Efetuar Pull** gerarão exceções se você testá-los. Na próxima seção, você conectará esse aplicativo cliente ao serviço móvel novamente para testar as operações **Push** e**Pull** para sincronizar o armazenamento com o banco de dados do serviço móvel.


1. No Gerenciador de Soluções do Visual Studio, abra App.xaml.cs no projeto compartilhado. Altere a inicialização do **MobileServiceClient** para um endereço inválido substituindo "**azure-mobile.net**" por "**azure-mobile.xxx**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. No Visual Studio, pressione **F5** para compilar e executar o aplicativo. Insira alguns novos todoitems e clique em **Salvar** para cada um. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente comporta-se como se estivesse conectado ao serviço móvel oferecendo suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

    ![][4]

3. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu serviço móvel

Nesta seção você reconecta o aplicativo ao serviço móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o serviço móvel.


1. No Gerenciador de Soluções do Visual Studio, abra App.xaml.cs no projeto compartilhado. Altere a inicialização do **MobileServiceClient** de volta para o endereço correto substituindo "**azure-mobile.xxx**" por "**azure-mobile.net**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Testar o aplicativo conectado ao serviço móvel


Nesta seção você testará as operações de push e pull para sincronizar o repositório local com o banco de dados do serviço móvel. Você pode testar o cliente Windows Store 8.1 ou Windows Phone 8.1. As capturas de tela abaixo mostram o cliente Windows Store 8.1. 

1. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao serviço móvel. Isso ocorre porque esse aplicativo sempre funciona com a `IMobileServiceSyncTable` que está apontada para o repositório local.

    ![][4]

2. Faça logon no Portal de Gerenciamento do Azure e procure o banco de dados de seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel. Se estiver usando o back-end do .NET para o serviço móvel, você poderá clicar no botão **Gerenciar** do banco de dados no SQL Azure Extension para executar uma consulta na tabela.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

    ![][6]

3. No aplicativo, pressione o botão  **Enviar por Push**. Isso faz com que o aplicativo chame `MobileServiceClient.SyncContext.PushAsync`. Essa operação push faz com que o banco de dados do serviço móvel receba os dados do repositório. No entanto, o repositório local não recebe os itens do banco de dados do serviço móvel.

    Uma operação de push é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre as tabelas.

    ![][7]

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens para concluí-los repositório local. 

    ![][8]

5. Desta vez, pressione o botão **Efetuar Pull** no aplicativo. O aplicativo chama `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`.  Observe que é efetuado pull de todos os dados do banco de dados do serviço móvel para o repositório local, conforme mostrado no aplicativo. No entanto, observe também que todos os dados do repositório local ainda foram enviados por push ao banco de dados do serviço móvel. Isso ocorre porque um **pull sempre executa um push primeiro**. Isso serve para garantir que todas as tabelas no armazenamento local, juntamente com os relacionamentos, estejam sincronizadas.
 
    ![][9]

    ![][10] 
  

## Resumo

Para dar suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando queremos sincronizar o repositório local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*    Para enviar por push as alterações ao servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados de alguma forma localmente (por meio de operações CRUD) serão enviados ao servidor.
   
* Para efetuar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Um pull sempre envia um push primeiro. Isso serve para garantir que todas as tabelas no armazenamento local, juntamente com os relacionamentos, estejam sincronizadas.

    Também há sobrecargas de `PullAsync()` que permitem que uma consulta seja especificada para dar suporte à sincronização incremental. Se uma consulta não for passada, `PullAsync()`receberá todas as linhas da tabela correspondentes (ou consulta). Você pode passar a consulta para filtrar apenas as alterações com as quais seu aplicativo precisa sincronizar.


## Próximas etapas

* [Tratando conflitos com o suporte offline para Serviços Móveis]

<!-- Anchors. -->
[Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
[Testar o aplicativo em um cenário offline]: #test-offline-app
[Atualizar o aplicativo para reconectar seu serviço móvel]: #update-online-app
[Testar o aplicativo conectado ao Serviço Móvel]: #test-online-app
[Próximas etapas]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Tratando conflitos com o suporte offline para Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Exemplo offline da TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Tutorial do Windows Phone 8 para o Visual Studio 2012]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Nuget de SDK para Serviços Móveis]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Nuget de armazenamento de SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
