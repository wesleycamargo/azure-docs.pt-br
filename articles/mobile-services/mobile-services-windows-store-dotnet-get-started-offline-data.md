<properties 
	pageTitle="Usando dados offline no seu aplicativo universal Windows | Serviços Móveis do Azure" 
	description="Aprenda a usar os Serviços Móveis do Azure para armazenar em cache e sincronizar dados offline no seu aplicativo universal Windows." 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="donnam"/>

# Usando sincronização de dados offline em Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Este tutorial mostra como adicionar suporte offline para um aplicativo Universal da Windows Store usando os Serviços móveis do Azure. Suporte offline permitirá que você interaja com um banco de dados local quando seu aplicativo estiver em um cenário offline. Quando seu aplicativo está online com o banco de dados back-end, você pode sincronizar as alterações locais usando recursos offline.

Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

Neste tutorial, você atualizará o projeto de aplicativo Universal desde o tutorial da [Introdução aos Serviços Móveis] para dar suporte aos recursos offline dos Serviços Móveis do Azure. Em seguida, você adicionará dados em um cenário offline desconectado, sincronizará esses itens ao banco de dados online e, em seguida, fará logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

>[AZURE.NOTE]O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem usar o Azure para armazenar e recuperar dados em um aplicativo da Windows Store. Se esta for sua primeira experiência com os Serviços Móveis, primeiro conclua o tutorial [Introdução aos Serviços Móveis].
>
>O tutorial mais antigo do Windows Phone 8 para Visual Studio 2012 ainda está disponível aqui, [Tutorial do Windows Phone 8 para o Visual Studio 2012].

##Pré-requisitos 

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão da [Introdução aos Serviços Móveis].
* [SDK dos Serviços móveis do Azure versão 1.3.0 (ou posterior)][Mobile Services SDK Nuget]
* [Armazenamento do SQLite dos Serviços Móveis do Azure versão 1.0.0 (ou posterior)][SQLite store nuget]
* [SQLite para Windows 8.1](www.sqlite.org/downloads)
* Uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28). 

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

Os recursos offline dos Serviços Móveis do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, você deve inicializar um `MobileServiceClient.SyncContext` para um armazenamento local. Em seguida, faça referência à sua tabela por meio da interface da `IMobileServiceSyncTable`. Neste tutorial, usamos o SQLite para o repositório local.

>[AZURE.NOTE]Você pode ignorar esta seção e obter apenas o projeto de exemplo que já tem o suporte offline do repositório de amostras GitHub para os Serviços Móveis. O projeto de exemplo com o suporte offline habilitado está localizado aqui, [Exemplo offline da TodoList].

1. Instale o tempo de execução do SQLite para Windows 8.1 e Windows Phone 8.1. 

    * **Tempo de Execução do Windows 8.1:** instalar o [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** instalar o [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE]Se você estiver usando o Internet Explorer, clicando no link para instalar o SQLite pode ser solicitado que você baixe o arquivo .vsix como um arquivo .zip. Salve o arquivo em um local no seu disco rígido com a extensão .vsix em vez de .zip. O duplo clique no arquivo .vsix no Windows Explorer para executar a instalação.

2. No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis]. Instale o pacote **WindowsAzure.MobileServices.SQLiteStore** do NuGet para o tempo de execução do Windows 8.1 e os projetos do Windows Phone 8.1.

    * **Windows 8.1:** no Gerenciador de Soluções, clique com botão direito do mouse no projeto do Windows 8.1 e clique em **Gerenciar Pacotes NuGet** para executar o Gerenciador de Pacotes do NuGet. Procure **SQLiteStore** para instalar o pacote do `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** clique com o botão direito do mouse no projeto do Windows Phone 8.1 e clique em **Gerenciar Pacotes Nuget** para executar o Gerenciador de Pacotes do NuGet. Procure **SQLiteStore** para instalar o pacote do `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Se a instalação criar uma referência a uma versão anterior do SQLite, apenas exclua essa referência duplicada.

    ![][2]

2. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** para o tempo de execução dos projetos da plataforma Windows 8.1 e Windows Phone 8.1 e adicione uma referência para o SQLite, que está localizado na seção **Extensões**.

    ![][1] </br>

    **Tempo de Execução do Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

3. O Tempo de Execução do SQLite requer que altere a arquitetura do processador do projeto que está sendo criado para **x86**, **x64** ou **ARM**. Não há suporte para **Qualquer CPU**. No Gerenciador de soluções, clique na solução na parte superior e mude a caixa suspensa da arquitetura processador para uma das configurações com suporte que você deseja testar.

    ![][13]

5. No Gerenciador de soluções, no projeto compartilhado, abra o arquivo MainPage.cs. Remova os comentários a seguir usando instruções no topo do arquivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Em MainPage.cs, comente a definição de `todoTable` e remova os comentários na linha a seguir que chama `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync


7. Em MainPage.cs, na região marcada como `Offline sync`, remova os métodos `InitLocalStoreAsync` e `SyncAsync`. O método `InitLocalStoreAsync` inicializa o contexto de sincronização do cliente com um armazenamento do SQLite.

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. No manipulador de eventos do `OnNavigatedTo`, remova a chamada para `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Remova os comentários das três chamadas para `SyncAsync` nos métodos `InsertTodoItem`, `UpdateCheckedTodoItem`, e `ButtonRefresh_Click`:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Adicione manipuladores de exceção ao método `SyncAsync`:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
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
        }

    Neste exemplo, recuperamos todos os registros da `todoTable` remota, mas também é possível filtrar registros passando uma consulta. O primeiro parâmetro para `PullAsync` é uma ID da consulta usada para sincronização incremental, que usa o carimbo de data/hora `UpdatedAt` para obter apenas os registros modificados desde a última sincronização. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `null` como a ID da consulta. Isso recuperará todos os registros de cada operação de pull, o que é potencialmente ineficiente.

    >[AZURE.NOTE]Para remover registros de armazenamento local do dispositivo quando eles tiverem sido excluídos do banco de dados do serviço móvel, você deve habilitar a [Exclusão Reversível]. Caso contrário, seu aplicativo deverá chamar `IMobileServiceSyncTable.PurgeAsync()` periodicamente para limpar o armazenamento local.

    Observe que `MobileServicePushFailedException` pode ocorrer devido a uma operação de push ou de pull. Ele pode ocorrer por uma recepção porque a operação de recepção executa internamente um envio para certificar-se de que todas as tabelas, juntamente com quaisquer relacionamentos serão consistentes. O próximo tutorial, [Tratando conflitos com o suporte offline para os Serviços Móveis], mostra como manipular essas exceções relacionadas à sincronização.

11. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo. O aplicativo se comportará como antes das alterações de sincronização offline, porque ele faz uma operação de sincronização nas operações Inserir, Update e Atualizar.

## <a name="update-sync"></a>Atualizar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para que ele não sincronize as operações de inserção e de atualização, mas somente quando o botão **Atualizar** for pressionado. Em seguida, você interromperá a conexão do aplicativo com o serviço móvel para simular um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no armazenamento local, mas não sincronizados com o serviço móvel.

1. Abra MainPage.cs no projeto compartilhado. Edite os métodos `InsertTodoItem` e `UpdateCheckedTodoItem` para comentar as chamadas para `SyncAsync`.

2. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione as linhas a seguir, que usam uma URL de serviço móvel inválida:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. Em `InitLocalStoreAsync()`, comente a chamada para `SyncAsync()`, de modo que o aplicativo não execute uma sincronização na inicialização.

4. Pressione **F5** para compilar e executar o aplicativo. Insira alguns novos itens de tarefas e clique em **Salvar** para cada um. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao serviço móvel oferecendo suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

5. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu serviço móvel

Nesta seção você reconecta o aplicativo ao serviço móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o serviço móvel. Quando você pressionar o botão Atualizar, dados serão sincronizados com o serviço móvel.

1. Abra o App.xaml.cs no projeto compartilhado. Remova o comentário de sua inicialização anterior do `MobileServiceClient` para adicionar novamente a URL e a chave de aplicativo do serviço móvel corretas.

2. Pressione a tecla **F5** para recompilar e executar o projeto. Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao serviço móvel. Isso ocorre porque esse aplicativo sempre funciona com a `IMobileServiceSyncTable` que está apontada para o repositório local.

3. Faça logon no Portal de Gerenciamento do Microsoft Azure e observe o banco de dados para seu serviço móvel. Se o serviço usar o back-end do JavaScript para serviços móveis, você poderá procurar os dados na guia **Dados** do serviço móvel.

    Se você estiver usando o back-end do .NET para o serviço móvel, no Visual Studio, vá para **Gerenciador de Servidores** -> **Azure** -> **Bancos de Dados SQL**. Clique com o botão direito do mouse em seu Banco de Dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

    ![][6]

4. No aplicativo, pressione o botão **Atualizar**. Isso faz com que o aplicativo chame `PushAsync` e `PullAsync`. Esta operação de push envia os itens do repositório local para o serviço móvel, então recupera novos dados do serviço móvel.

    Uma operação de push é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre tabelas.

    ![][7]

5. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

    ![][8]

6. Pressione o botão **Atualizar** novamente, que faz com que a `SyncAsync` seja chamada. A `SyncAsync` chama push e pull, mas nesse caso, poderíamos ter removido a chamada para `PushAsync`. Isso ocorre porque um **pull sempre executa um push primeiro**. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.

    ![][10]
  

##Resumo

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Próximas etapas

* [Tratando conflitos com o suporte offline para Serviços Móveis]

* [Usando a exclusão reversível nos Serviços Móveis][Soft Delete]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your mobile service]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Tratando conflitos com o suporte offline para Serviços Móveis]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Tratando conflitos com o suporte offline para os Serviços Móveis]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Exemplo offline da TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Introdução aos Serviços Móveis]: ../mobile-services-windows-store-get-started.md
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Tutorial do Windows Phone 8 para o Visual Studio 2012]: mobile-services-windows-phone-get-started-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[Exclusão Reversível]: mobile-services-using-soft-delete.md


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
 

<!---HONumber=August15_HO6-->