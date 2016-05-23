<properties
	pageTitle="Habilitar sincronização offline para seu aplicativo móvel do Azure (Windows 8.1) | Microsoft Azure"
	description="Saiba como usar o aplicativo móvel do Azure para cache e sincronização de dados offline no seu aplicativo da Windows Store"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Habilitar sincronização offline para o seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral

Este tutorial mostra como adicionar suporte offline para um aplicativo do Windows 8.1 Store ou Phone usando o back-end de aplicativos móveis do Azure. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações serão sincronizadas ao serviço remoto.

Neste tutorial, você atualizará o projeto de aplicativo do Windows 8.1 do tutorial [Criar um aplicativo do Windows] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## Requisitos

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão de [Criar um aplicativo do Windows][create a windows app].
* [Armazenamento do SQLite dos Serviços Móveis Azure][sqlite store nuget]
* [SQLite para Windows 8.1](http://www.sqlite.org/downloads)

## Atualize o aplicativo cliente para dar suporte aos recursos offline

Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você deve inicializar um `MobileServiceClient.SyncContext` para um armazenamento local. Em seguida, faça referência à sua tabela por meio da interface da `IMobileServiceSyncTable`. Neste tutorial, usamos o SQLite para o repositório local.

1. Instale o tempo de execução do SQLite para Windows 8.1 e Windows Phone 8.1.

    * **Tempo de Execução do Windows 8.1:** instalar o [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** instalar o [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE] Essas instruções também se aplicam a projetos do Windows 10 UAP, mas em vez disso, você deve instalar o [SQLite para Windows 10].

2. No Visual Studio, abra o projeto que você concluiu no tutorial [Criar um aplicativo do Windows]. Instale o pacote **Microsoft.Azure.Mobile.Client.SQLiteStore** do NuGet para o tempo de execução do Windows 8.1 e os projetos do Windows Phone 8.1. Adicione a referência do NuGet aos projetos do Windows Store 8.1 e do Windows Phone 8.1.

    >[AZURE.NOTE] Se a instalação criar uma referência adicional para uma versão do SQLite diferente da que você instalou, você receberá um erro de compilação. Você deve resolver esse erro removendo a duplicação no nó **Referências** em seus projetos.

3. SQLite é uma biblioteca nativa e requer que você escolha uma arquitetura de plataforma específica como **x86**, **x64** ou **ARM**. Não há suporte para **Qualquer CPU**. No Gerenciador de soluções, clique na solução na parte superior e mude a caixa suspensa da arquitetura processador para uma das configurações com suporte que você deseja testar.

    >[AZURE.NOTE] Se você estiver usando o Visual Studio 2015, clique com o botão direito do mouse na solução e, em seguida, clique em **Propriedades** para abrir o Gerenciador de Configuração para definir a plataforma para os projetos do Windows e do Windows Phone.

    ![][13]


4. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** para os projetos de plataforma do Windows 8.1 Runtime e do Windows Phone 8.1. Verifique se há uma referência ao SQLite, que está localizado na seção **Extensões**.

    >[AZURE.NOTE] Se você estiver usando o Visual Studio 2015, clique com o botão direito do mouse no nó **Referências** para os projetos da plataforma do Windows 8.1 Runtime e do Windows Phone 8.1, e clique em **Adicionar uma referência** para abrir o Gerenciador de Referências.


    ![][1] </br>

    **Tempo de Execução do Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**


5. No Gerenciador de soluções, no projeto compartilhado, abra o arquivo MainPage.cs. Remova os comentários a seguir usando instruções no topo do arquivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Em MainPage.cs, comente a linha de código que inicializa `todoTable` como um `IMobileServiceTable`. Remova os comentários da linha de código que inicializa `todoTable` como um `IMobileServiceSyncTable`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. Em MainPage.cs, na região marcada como `Offline sync`, remova os métodos `InitLocalStoreAsync` e `SyncAsync`. O método `InitLocalStoreAsync` inicializa o contexto de sincronização do cliente com um armazenamento do SQLite. No Visual Studio, é possível selecionar todas as linhas comentadas e usar o atalho de teclado **Ctrl**+**K**+**U** para remover os comentários.

	Observe que em `SyncAsync`, uma operação de push é executada por `MobileServiceClient.SyncContext` em vez de `IMobileServicesSyncTable`. Isso ocorre porque o contexto controla as alterações feitas pelo cliente para todas as tabelas. Para obter mais informações sobre esse comportamento, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].

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

10. Adicione manipuladores de exceção ao método `SyncAsync`. Em uma situação offline, um `MobileServicePushFailedException` será lançado com `PushResult.Status == CancelledByNetworkError`.

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
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
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

    Neste exemplo de `PullAsync`, recuperamos todos os registros no `todoTable` remoto, mas também é possível filtrar os registros realizando uma consulta. O primeiro parâmetro para `PullAsync` é uma ID da consulta usada para sincronização incremental, que usa o carimbo de data/hora `UpdatedAt` para obter apenas os registros modificados desde a última sincronização. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo cliente. Se desejar sair da sincronização incremental, passe `null` como a ID da consulta. Isso recuperará todos os registros de cada operação de pull, o que é potencialmente ineficiente.

    Observe que `MobileServicePushFailedException` pode ocorrer devido a uma operação de push ou de pull. Ele pode ocorrer por uma recepção porque a operação de recepção executa internamente um envio para certificar-se de que todas as tabelas, juntamente com quaisquer relacionamentos serão consistentes.

11. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo se comportará como antes das alterações de sincronização offline, porque ele faz uma operação de sincronização nas operações Inserir, Update e Atualizar. No entanto, ele preencherá um banco de dados local que pode ser usado em um cenário offline. Colocaremos o cenário offline na próxima seção agora que o banco de dados local é preenchido.

## <a name="update-sync"></a>Atualize o comportamento de sincronização do aplicativo cliente

Nesta seção, você modificará o aplicativo cliente para simular um cenário offline quebrando a conexão com o back-end do aplicativo móvel do Azure. Quando você adicionar itens de dados, o manipulador de exceção informará que o aplicativo está operando no modo offline com `PushResult.Status == CancelledByNetworkError`. Itens adicionados serão mantidos no armazenamento local, mas não sincronizados no back-end do aplicativo móvel até você ficar online novamente e executar um push bem-sucedido para o back-end do aplicativo móvel do Azure.

1. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione as seguintes linhas, que usam uma URL de aplicativo móvel inválido:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Observe que quando seu aplicativo também estiver usando a autenticação, isso causará a falha da entrada. Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celulares no dispositivo ou usando o modo avião.

2. Pressione **F5** para compilar e executar o aplicativo. Observe a falha de sincronização na atualização, quando o aplicativo é iniciado.
3. Insira alguns novos itens de tarefas e clique em **Salvar** para cada um. Ocorre falha no envio por push para cada um com um `PushResult.Status=CancelledByNetworkError`. Os novos itens de tarefas pendentes existem apenas no armazenamento local até que possam ser enviados por push para o back-end do aplicativo móvel.

	É possível suprimir a caixa de diálogo de exceção para `PushResult.Status=CancelledByNetworkError`, então o aplicativo cliente se comportaria como se estivesse conectado ao back-end do aplicativo móvel que dá suporte a todas as operações CRUD (criar, ler, atualizar, excluir) diretamente.

4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

5. (Opcional) No Visual Studio, abra o **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de Dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foram alterados.

6. (Opcional) Use uma ferramenta REST, como o Fiddler ou Postman, para consultar seu back-end móvel, usando uma consulta GET no formulário `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualize o aplicativo para reconectar o back-end do Aplicativo Móvel

Nesta seção, você reconecta o aplicativo ao back-end do aplicativo móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o back-end do aplicativo móvel. Quando você executar o aplicativo pela primeira vez, o manipulador de eventos de `OnNavigatedTo` chamará `InitLocalStoreAsync`. Isso, por sua vez, chamará `SyncAsync` para sincronizar seu armazenamento local com o banco de dados de back-end. Então, o aplicativo tentará sincronizar na inicialização.

1. Abra o App.xaml.cs no projeto compartilhado. Remova o comentário de sua inicialização anterior do `MobileServiceClient` para usar as URLs corretas do aplicativo móvel e do gateway.

2. Pressione a tecla **F5** para recompilar e executar o projeto. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull assim que o manipulador de eventos de `OnNavigatedTo` é executado.

3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

  `UpdateCheckedTodoItem` chama `SyncAsync` para sincronizar cada item concluído com o back-end do aplicativo móvel. `SyncAsync` chama push e pull. No entanto, você deve observar que, **sempre que executar um pull em comparação a uma tabela em que o cliente fez alterações, um push no contexto de sincronização do cliente sempre será executado primeiro automaticamente**. Isso serve para garantir que todas as tabelas no repositório local, juntamente com as relações, permaneçam consistentes. Então nesse caso, poderíamos ter removido a chamada a `PushAsync` porque ela é executada automaticamente ao executar um pull. Esse comportamento pode resultar em um push inesperado se você não estiver ciente dele. Para obter mais informações sobre esse comportamento, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].


##Resumo

Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Para executar push das mudanças no servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:

    Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

* Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.

    Um pull sempre emitirá um push de contexto de sincronização primeiro se o contexto de sincronização do cliente tiver alterações controladas na tabela. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.

    Também há sobrecargas de `PullAsync()` que permitem que uma consulta seja especificada para filtrar os dados armazenados no cliente. Se uma consulta não for passada, o `PullAsync()` efetuará a recepção de todas as linhas da tabela correspondente (ou consulta). Você pode passar a consulta para filtrar apenas as alterações que seu aplicativo precisa para sincronizar com.

* Para habilitar a sincronização incremental, passe uma ID de consulta para `PullAsync()`. A ID de consulta é usada para armazenar o último carimbo de data/hora atualizado dos resultados da última operação de recepção. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se a consulta tiver um parâmetro, o mesmo valor de parâmetro poderá ser parte da ID da consulta.

    Por exemplo, se você estiver filtrando userid, sua ID de consulta única poderá ser:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Se você deseja recusar a sincronização incremental, passe `null` como a ID da consulta. Nesse caso, todos os registros serão recuperados em cada chamada de `PullAsync`, que é potencialmente ineficiente.

* Seu aplicativo deve chamar periodicamente `IMobileServiceSyncTable.PurgeAsync()` para limpar o armazenamento local.


## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] (observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)

* [Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronização de dados offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[Criar um aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite para Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921

[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/

[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0511_2016-->