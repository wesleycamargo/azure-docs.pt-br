<properties
	pageTitle="Usando dados offline com o aplicativo móvel do Azure (Windows Store) | Centro de desenvolvimento móvel"
	description="Saiba como usar o aplicativo móvel do Azure para cache e sincronização de dados offline no seu aplicativo da Windows Store"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Usando a sincronização de dados offline em aplicativos móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


Este tutorial mostra como adicionar suporte offline para um aplicativo Universal da Windows Store usando o back-end de aplicativos móveis do Azure. O suporte off-line permitirá que você interaja com um banco de dados local quando seu aplicativo estiver em um cenário offline. Quando seu aplicativo estiver online com o banco de dados back-end, você pode sincronizar as alterações locais usando os recursos offline.



Neste tutorial, você atualizará o projeto de aplicativo universal do tutorial [Criar um aplicativo do Windows] para dar suporte a recursos offline dos aplicativos móveis do Azure. Em seguida, você adicionará dados em um cenário offline desconectado, sincronizará esses itens ao banco de dados online e, em seguida, fará logon no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para dar suporte aos recursos offline]
2. [Atualizar o comportamento de sincronização do aplicativo]
3. [Atualizar o aplicativo para reconectar seu back-end de aplicativos móveis]

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão de [Criar um aplicativo do Windows].
* [SDK dos Serviços móveis do Azure versão 2.0.0 (ou posterior)][Azure Mobile App SDK Nuget]
* [Armazenamento do SQLite dos Serviços Móveis do Azure versão 1.0.2 (ou posterior)][SQLite store Nuget]
* [SQLite para Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Para concluir este tutorial, será necessária uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação Gratuita do Azure</a>.

## <a name="enable-offline-app"></a>Atualizar o aplicativo para dar suporte aos recursos offline

Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline com seu Serviço Móvel. Para usar esses recursos em seu aplicativo, você deve inicializar um `MobileServiceClient.SyncContext` para um armazenamento local. Em seguida, faça referência à sua tabela por meio da interface da `IMobileServiceSyncTable`. Neste tutorial, usamos o SQLite para o repositório local.

1. Instale o tempo de execução do SQLite para Windows 8.1 e Windows Phone 8.1.

    * **Tempo de Execução do Windows 8.1:** instalar o [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** instalar o [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE]Se você estiver usando o Internet Explorer, clicando no link para instalar o SQLite pode ser solicitado que você baixe o arquivo .vsix como um arquivo .zip. Salve o arquivo em um local no seu disco rígido com a extensão .vsix em vez de .zip. O duplo clique no arquivo .vsix no Windows Explorer para executar a instalação.

2. No Visual Studio, abra o projeto que você concluiu no tutorial [Criar um aplicativo do Windows]. Instale o pacote **WindowsAzure.MobileServices.SQLiteStore** do NuGet para o tempo de execução do Windows 8.1 e os projetos do Windows Phone 8.1.

    * **Windows 8.1:** no Gerenciador de Soluções, clique com botão direito do mouse no projeto do Windows 8.1 e clique em **Gerenciar Pacotes NuGet** para executar o Gerenciador de Pacotes do NuGet. Procure **SQLiteStore** para instalar o pacote do `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** clique com o botão direito do mouse no projeto do Windows Phone 8.1 e clique em **Gerenciar Pacotes Nuget** para executar o Gerenciador de Pacotes do NuGet. Procure **SQLiteStore** para instalar o pacote do `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Se a instalação criar uma referência a uma versão anterior do SQLite, apenas exclua essa referência duplicada.

3. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** para o tempo de execução dos projetos da plataforma Windows 8.1 e Windows Phone 8.1 e adicione uma referência para o SQLite, que está localizado na seção **Extensões**.

    ![][1] </br>

    **Tempo de Execução do Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. O Tempo de Execução do SQLite requer que altere a arquitetura do processador do projeto que está sendo criado para **x86**, **x64** ou **ARM**. Não há suporte para **Qualquer CPU**. No Gerenciador de soluções, clique na solução na parte superior e mude a caixa suspensa da arquitetura processador para uma das configurações com suporte que você deseja testar.

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

    Observe que `MobileServicePushFailedException` pode ocorrer devido a uma operação de push ou de pull. Ele pode ocorrer por uma recepção porque a operação de recepção executa internamente um envio para certificar-se de que todas as tabelas, juntamente com quaisquer relacionamentos serão consistentes.

11. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo. O aplicativo se comportará como antes das alterações de sincronização offline, porque ele faz uma operação de sincronização nas operações Inserir, Update e Atualizar.

## <a name="update-sync"></a>Atualizar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para que ele não sincronize as operações de inserção e de atualização, mas somente quando o botão **Atualizar** for pressionado. Em seguida, você interromperá a conexão do aplicativo com o back-end móvel para simular um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no armazenamento local, mas não sincronizados com o back-end do aplicativo móvel.

1. Abra o App.xaml.cs no projeto compartilhado. Edite os métodos `InsertTodoItem` e `UpdateCheckedTodoItem` para comentar as chamadas para `SyncAsync`.

2. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione as seguintes linhas, que usam uma URL de aplicativo móvel inválido:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. Em `InitLocalStoreAsync()`, comente a chamada para `SyncAsync()`, de modo que o aplicativo não execute uma sincronização na inicialização.

4. Pressione **F5** para compilar e executar o aplicativo. Insira alguns novos itens de tarefas e clique em **Salvar** para cada um. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o back-end do aplicativo móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end do aplicativo móvel dando suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

5. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu back-end do aplicativo móvel

Nesta seção, você reconecta o aplicativo ao back-end do aplicativo móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o back-end do aplicativo móvel. Quando você pressionar o botão Atualizar, dados serão sincronizados com o back-end do aplicativo móvel.

1. Abra o App.xaml.cs no projeto compartilhado. Remova o comentário de sua inicialização anterior de `MobileServiceClient` para adicionar novamente a chave de aplicativo, a URL de gateway e a URL do back-end do serviço de aplicativo móvel corretos.

2. Pressione a tecla **F5** para recompilar e executar o projeto. Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao back-end do aplicativo móvel. Isso ocorre porque esse aplicativo sempre funciona com a `IMobileServiceSyncTable` que está apontada para o repositório local.

3. Faça logon no portal do Azure e examine o banco de dados para o back-end do aplicativo móvel.

    No Visual Studio, vá para **Gerenciador de Servidores** -> **Azure** -> **Bancos de Dados SQL**. Clique com o botão direito do mouse em seu Banco de Dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**.

    Observe que os dados não foram sincronizados entre o banco de dados e o repositório local.

4. No aplicativo, pressione o botão **Atualizar**. Isso faz com que o aplicativo chame `PushAsync` e `PullAsync`. Esta operação de push envia os itens do repositório local para o serviço móvel, então recupera novos dados do serviço móvel.

    Uma operação de push é executada fora do `MobileServiceClient.SyncContext` em vez do `IMobileServicesSyncTable` e envia por push as alterações em todas as tabelas associadas a esse contexto de sincronização. Isso é para cobrir cenários onde há relacionamentos entre tabelas.

5. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

6. Pressione o botão **Atualizar** novamente, que faz com que a `SyncAsync` seja chamada. A `SyncAsync` chama push e pull, mas nesse caso, poderíamos ter removido a chamada para `PushAsync`. Isso ocorre porque um **pull sempre executa um push primeiro**. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.


##Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Para executar push das mudanças no servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

* Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Uma pull sempre envia um push primeiro. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.

    Também há sobrecargas de `PullAsync()` que permitem que uma consulta seja especificada para filtrar os dados armazenados no cliente. Se uma consulta não for passada, o `PullAsync()` efetuará a recepção de todas as linhas da tabela correspondente (ou consulta). Você pode passar a consulta para filtrar apenas as alterações que seu aplicativo precisa para sincronizar com.

* Para habilitar a sincronização incremental, passe uma ID de consulta para `PullAsync()`. A ID de consulta é usada para armazenar o último carimbo de data/hora atualizado dos resultados da última operação de recepção. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se a consulta tiver um parâmetro, então o mesmo valor de parâmetro deve ser parte da ID da consulta.

    Por exemplo, se você estiver filtrando userid, ele precisa fazer parte da ID da consulta:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Se você deseja recusar a sincronização incremental, passe `null` como a ID da consulta. Nesse caso, todos os registros serão recuperados em cada chamada de `PullAsync`, que é potencialmente ineficiente.

* Seu aplicativo deve chamar periodicamente `IMobileServiceSyncTable.PurgeAsync()` para limpar o armazenamento local.


<!-- Anchors. -->
[Atualizar o aplicativo para dar suporte aos recursos offline]: #enable-offline-app
[Atualizar o comportamento de sincronização do aplicativo]: #update-sync
[Atualizar o aplicativo para reconectar seu back-end de aplicativos móveis]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[TodoList Offline Sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[Criar um aplicativo do Windows]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[Azure Mobile App SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=62-->