<properties urlDisplayName="Using Offline Data" pageTitle="Usando dados offline nos Serviços móveis (Windows Store) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como usar os Serviços Móveis do Azure para chave e sincronização de dados offline no seu aplicativo da Windows Store" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Usando a sincronização de dados offline nos Serviços móveis

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar suporte offline para um aplicativo Universal da Windows Store usando os Serviços móveis do Azure. O suporte off-line permitirá que você interaja com um banco de dados local quando seu aplicativo estiver em um cenário offline. Quando seu aplicativo estiver online com o banco de dados back-end, você pode sincronizar as alterações locais usando os recursos offline. 
</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">14:36</span></div>
</div>


Neste tutorial, você irá atualizar o projeto de aplicativo Universal desde o tutorial de [Introdução aos serviços móveis] para oferecer suporte aos recursos offline dos Serviços móveis do Azure. Você irá adicionar dados em um cenário desconectado offline, sincronizar esses itens ao banco de dados online e, em seguida, fazer logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.


>[WACOM.NOTE] O objetivo deste tutorial é ajudá-lo a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Se esta for sua primeira experiência com os Serviços móveis, você deve concluir o tutorial [Introdução aos serviços móveis] primeiro.
>
>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>. 
>
>O tutorial mais antigo do Windows Phone 8 para Visual Studio 2012 ainda está disponível aqui, [Tutorial do Windows Phone 8 para o Visual Studio 2012].


Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para dar suporte aos recursos offline]
2. [Testar o aplicativo em um cenário offline] 
3. [Atualizar o aplicativo para reconectar seu serviço móvel]
4. [Testar o aplicativo conectado ao serviço móvel]

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão da [Introdução aos Serviços móveis].
* [SDK dos Serviços móveis do Azure versão 1.3.0 (ou posterior)][SDK dos Serviços móveis do Nuget]
* [Repositório do SQLite versão 1.0.0 (ou posterior) dos Serviços móveis do Azure][Repositório do SQLite do Nuget]
* SQLite para Windows 8.1

>[AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>. 

## <a name="enable-offline-app"></a>Atualizar o aplicativo para oferecer suporte a recursos offline

Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, inicialize um `MobileServiceClient.SyncContext` para um repositório local. Faça referência à sua tabela através da interface `IMobileServiceSyncTable`. Neste tutorial, usamos o SQLite para o repositório local.

>[AZURE.OBSERVAÇÃO] você pode ignorar esta seção e obter apenas o projeto de exemplo que já tem o suporte offline do repositório Github exemplos para os Serviços móveis. O projeto de exemplo com o suporte offline ativado está localizado aqui, [exemplo Offline TodoList].


1. Instale o tempo de execução do SQLite para Windows 8.1 e Windows Phone 8.1. 

    * **Tempo de execução do Windows 8.1:** Instale o tempo de execução do SQLite para Windows 8.1 a partir deste link, [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** Instale o tempo de execução do SQLite para Windows Phone 8.1 a partir deste link, [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE] Se você estiver usando o Internet Explorer, clicando no link para instalar o SQLite pode ser solicitado que você baixe o arquivo .vsix como um arquivo .zip. Salve o arquivo em um local no seu disco rígido com a extensão .vsix em vez de .zip. O duplo clique no arquivo .vsix no Windows Explorer para executar a instalação.

2. No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços móveis]. No Gerenciador de soluções, clique com o botão direito em **Referências** para o tempo de execução dos projetos da plataforma Windows 8.1 e Windows Phone 8.1 e adicione uma referência para o SQLite, que está localizado na seção **Extensões**. 

    ![][1]
    </br>**Tempo de execução do Windows 8.1:**

    ![][11]
    </br>**Windows Phone 8.1**

3. O Tempo de Execução do SQLite requer a alteração da arquitetura do processador do projeto que está sendo criado para **x86**, **x64** ou **ARM**. Não há suporte para **Qualquer CPU**. No Gerenciador de soluções do Visual Studio, clique na solução na parte superior e mude a caixa suspensa da arquitetura processador para uma das configurações com suporte que você deseja testar.

    ![][13]

4. Instale o pacote **WindowsAzure.MobileServices.SQLiteStore** do NuGet para o tempo de execução dos projetos do Windows 8.1 e do Windows Phone 8.1.

    * **Windows 8.1:** No Gerenciador de soluções, clique com botão direito no projeto do Windows 8.1 e clique em **Gerenciador de pacotes do Nuget** para executar o Gerenciador de pacotes do NuGet. Procure por **SQLiteStore** para instalar o pacote WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8.1:** Clique com botão direito no projeto do Windows Phone 8.1 e clique em **Gerenciador de pacotes do NuGet** para executar o Gerenciador de pacotes do NuGet. Procure por **SQLiteStore** para instalar o pacote WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Se a instalação criar uma referência a uma versão anterior do SQLite, apenas exclua essa referência duplicada. 

    ![][2]

5. No Gerenciador de soluções do Visual Studio, no projeto compartilhado, abra o arquivo MainPage.cs. Adicione o seguinte usando as instruções na parte superior do arquivo.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. No arquivo compartilhado, Mainpage.cs, substitua a declaração `todoTable` com uma declaração do tipo `IMobileServicesSyncTable` que é inicializado chamando `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. No arquivo compartilhado, MainPage.cs, atualize o manipulador de eventos 'OnNavigatedTo' para que ele inicialize o contexto de sincronização do cliente em um repositório do SQLite. O repositório do SQLite é criado com uma tabela que corresponde ao esquema da tabela de serviço móvel e deve incluir a propriedade da **Versão** do sistema que você irá adicionar na próxima etapa.

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


8. No Gerenciador de soluções do Visual Studio, no projeto compartilhado, expanda **DataModel** e abra TodoItem.cs. Adicione uma declaração 'using' para o namespace MobileServices. Isso é necessário para solucionar o atributo da versão para a propriedade de versão do sistema.

        using Microsoft.WindowsAzure.MobileServices;

      Atualize a classe 'TodoItem' para que a classe inclua a propriedade da **Versão** do sistema da seguinte maneira. O esquema da tabela deve incluir a propriedade da **Versão** do sistema conforme mostrado aqui para oferecer suporte aos recursos offline.

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




9. Em seguida, atualize a interface do usuário para os projetos Windows 8.1 e Windows Phone 8.1 para incluir botões que darão suporte as operações de sincronização offline entre o banco de dados offline local e o banco de dados do Serviço móvel no Azure. 

    * **Windows 8.1:** No Gerenciador de soluções do Visual Studio, no projeto do Windows 8.1, abra MainPage. xaml. Localize o botão chamado **ButtonRefresh**. Substitua esse elemento de botão com o seguinte painel de pilha de botões. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1:** No Gerenciador de soluções do Visual Studio, no projeto do Windows 8.1, abra MainPage. xaml. Localize o botão chamado **ButtonRefresh**. Substitua esse elemento de botão com o seguinte painel de pilha de botões. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. Adicione o arquivo compartilhado MainPage.cs, clique em manipuladores de eventos para os botões **Enviar por Push** e **Efetuar Pull**. Em seguida, salve o arquivo. 
        
    Observe que o `MobileServicePushFailedException` pode ocorrer devido a uma operação de envio e recepção. Ele pode ocorrer por uma recepção porque a operação de recepção executa internamente um envio para certificar-se de que todas as tabelas, juntamente com quaisquer relacionamentos serão sincronizados. O próximo tutorial, [Tratando conflitos com o suporte offline para Serviços móveis], mostra como manipular essas exceções relacionadas com a sincronização.

    Para oferecer suporte à sincronização dos registros excluídos com a sincronização de dados offline, você deve habilitar [Exclusão reversível](/ pt-br/documentation/articles/mobile-services-using-soft-delete/). Caso contrário, você precisa remover manualmente os registros no armazenamento local ou chamar `IMobileServiceSyncTable::PurgeAsync()` para limpar o armazenamento local.

    Nesse exemplo, passamos uma consulta para a chamada do método `PullAsync` para dar suporte à sincronização incremental. Isso é útil em casos onde você não quer a despesa de receber a tabela inteira durante a sincronização. Neste cenário, não estamos preocupados com mudanças do texto para os todoitems, apenas queremos puxar os itens concluídos para marcá-los fora da nossa lista de tarefas.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // All items should be synced since other clients might mark an item as complete
                // The first parameter is a query ID that uniquely identifies the query.
                // This is used in incremental sync to get only newer items the next time PullAsync is called
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery());

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

11. Crie a solução e verifique se não ocorreu nenhum erro de compilação em qualquer um dos projetos.


## <a name="test-offline-app"></a>Testar o aplicativo em um cenário offline

Nesta seção, você interrompe a conexão do aplicativo com o serviço móvel para simular um cenário offline. Em seguida, você adiciona alguns itens de dados que serão mantidos no armazenamento local.

Observe que, nesta seção, o aplicativo não deve estar conectado a nenhum serviço móvel. Portanto, os botões **Enviar por Push** e **Efetuar Pull** gerarão exceções se você testá-los. Na próxima seção, você conectará esse aplicativo cliente ao serviço móvel novamente para testar as operações **Push** e **Pull** para sincronizar o armazenamento com o banco de dados do serviço móvel.


1. No Gerenciador de soluções do Visual Studio, abra App.xaml.cs no projeto compartilhado. Altere a inicialização do **MobileServiceClient** para um endereço inválido substituindo "**azure-mobile.net**" por "**azure-mobile.xxx**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. No Visual Studio, pressione **F5** para compilar e executar o aplicativo. Insira alguns novos itens de tarefas e clique em **Salvar** para cada um deles. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao serviço móvel oferecendo suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

    ![][4]

3. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu serviço móvel

Nesta seção você reconecta o aplicativo ao serviço móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o serviço móvel.


1. No Gerenciador de soluções do Visual Studio, abra App.xaml.cs no projeto compartilhado. Altere a inicialização do **MobileServiceClient** de volta para o endereço correto substituindo "**azure-mobile.xxx**" por "**azure-mobile.net**" para sua URL. Em seguida, salve o arquivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Testar o aplicativo conectado ao serviço móvel


Nesta seção você testará as operações de push e pull para sincronizar o repositório local com o banco de dados do serviço móvel. Você pode testar o cliente da Windows Store 8.1 ou o cliente do Windows Phone 8.1. As capturas da tela abaixo mostram o cliente da Windows Store 8.1. 

1. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo. Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao serviço móvel. Isso ocorre porque esse aplicativo sempre funciona com a `IMobileServiceSyncTable` que está apontada para o repositório local.

    ![][4]

2. Faça logon no Portal de Gerenciamento do Microsoft Azure e observe o banco de dados para seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel. Se estiver usando o back-end do .NET para o serviço móvel, você poderá clicar no botão **Gerenciar** do banco de dados no SQL Azure Extension para executar uma consulta na tabela.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

    ![][6]

3. No aplicativo, pressione o botão **Enviar por Push**. Isso faz com que o aplicativo chame `MobileServiceClient.SyncContext.PushAsync`. Essa operação push faz com que o banco de dados do serviço móvel receba os dados do repositório. No entanto, o repositório local não recebe os itens do banco de dados do serviço móvel.

    Uma operação de envio for executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia as alterações para todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre tabelas.

€

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local. 

    ![][8]

5. Desta vez, pressione o botão **Receber** no aplicativo. O aplicativo chama `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`.  Observe que é efetuado pull de todos os dados do banco de dados do serviço móvel para o repositório local, conforme mostrado no aplicativo. No entanto, observe também que todos os dados do repositório local ainda foram enviados por push ao banco de dados do serviço móvel. Isso ocorre porque um **pull sempre executa um push primeiro**. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos estão sincronizados.
 
    ![][9]

    ![][10] 
  

##Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface "IMobileServiceSyncTable" e inicializamos "MobileServiceClient.SyncContext" com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o repositório local com o servidor, usamos os métodos "IMobileServiceSyncTable.PullAsync" e "MobileServiceClient.SyncContext.PushAsync".

*  Para enviar por push as mudanças para o servidor, chamamos "IMobileServiceSyncContext.PushAsync()". Esse método é um membro de "IMobileServicesSyncContext" em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados de alguma forma localmente (por meio de operações CRUD) serão enviados ao servidor.
   
* Para efetuar pull de dados de uma tabela no servidor para o aplicativo, chamamos "IMobileServiceSyncTable.PullAsync".

    Uma pull sempre envia um push primeiro. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos estão sincronizados.

    Também há sobrecargas de `PullAsync()` que permitem que uma consulta seja especificada para dar suporte à sincronização incremental. Se uma consulta não for passada, o `PullAsync()` efetuará a recepção de todas as linhas da tabela correspondente (ou consulta). Você pode passar a consulta para filtrar apenas as mudanças que seu aplicativo precisa sincronizar.


## Próximas etapas

* [Tratando conflitos com o suporte offline para os Serviços móveis]

<!-- Anchors. -->
[Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
[Testar o aplicativo em um cenário offline]: #test-offline-app
[Atualizar o aplicativo para reconectar seu serviço móvel]: #update-online-app
[Testar o aplicativo conectado ao serviço móvel]: #test-online-app
[Próximas etapas]: #next-steps

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
[Tratando conflitos com o suporte offline para os Serviços móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Exemplo Offline TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
[ SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Tutorial do Windows Phone 8 para Visual Studio 2012]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Nuget SDK dos Serviços móveis]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Nuget do repositório do SQLite ]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0

<!--HONumber=35.2-->
