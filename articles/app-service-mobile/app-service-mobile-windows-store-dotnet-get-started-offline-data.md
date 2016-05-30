<properties
	pageTitle="Habilitar sincronização offline para seu aplicativo UWP (Plataforma Universal do Windows) com os Aplicativos Móveis | Serviço de Aplicativo do Azure"
	description="Aprenda a usar um Aplicativo Móvel do Azure para armazenar em cache e sincronizar dados offline no seu aplicativo UWP (Plataforma Universal do Windows)."
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
	ms.date="05/14/2016"
	ms.author="wesmc"/>

# Habilitar sincronização offline para o seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral

Este tutorial mostra como adicionar suporte offline para um aplicativo da UWP (Plataforma Universal do Windows) usando o back-end de aplicativos móveis do Azure. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações serão sincronizadas ao serviço remoto.

Neste tutorial, você atualizará o projeto de aplicativo da UWP do tutorial [Criar um aplicativo do Windows] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## Requisitos

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão de [Criar um aplicativo do Windows][create a windows app].
* [Armazenamento do SQLite dos Serviços Móveis Azure][sqlite store nuget]
* [SQLite para desenvolvimento na Plataforma Universal do Windows](http://www.sqlite.org/downloads)

## Atualize o aplicativo cliente para dar suporte aos recursos offline

Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você deve inicializar um [SyncContext][synccontext] para um repositório local. Referencie sua tabela pela interface [IMobileServiceSyncTable][IMobileServiceSyncTable]. O SQLite é usado como o repositório local no dispositivo.

1. Instale o [tempo de execução do SQLite para a Plataforma Universal do Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. No Visual Studio, abra o gerenciador de pacotes do NuGet para o projeto de aplicativo da UWP que você concluiu no tutorial de [Criar um aplicativo Windows], procure o pacote do NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** e instale-o.

4. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** > **Universal do Windows** > **Extensões** e habilite **SQLite para Plataforma Universal do Windows** e **Tempo de Execução do Visual C++ 2015 para aplicativos da Plataforma Universal do Windows**.

    ![Adicionar referência SQLite UWP][1]

5. Abra o arquivo MainPage.xaml.cs e remova a marca de comentário das seguintes instruções `using` na parte superior do arquivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         

6. Comente a linha de código que inicializa `todoTable` como um **IMobileServiceTable**, em seguida, remova a marca de comentário da linha de código que inicializa `todoTable` como um **IMobileServiceSyncTable**:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 

7. Na região `Offline sync` do MainPage.xaml.cs, remova a marca de comentário dos seguintes métodos:

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

	No `SyncAsync`, uma operação de push é iniciada do [SyncContext][synccontext] seguido por uma sincronização incremental. O contexto de sincronização controla alterações feitas pelo cliente para todas as tabelas. Para obter mais informações sobre esse comportamento, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].

8. No manipulador de eventos do `OnNavigatedTo`, remova a marca de comentário da chamada para `InitLocalStoreAsync`. Se você já tiver concluído a [tutorial de autenticação](app-service-mobile-windows-store-dotnet-get-started-users.md), deverá fazer isso no método `AuthenticateAsync`.

9. Remova as marcas de comentário de chamadas para [PushAsync] nos métodos `InsertTodoItem` e `UpdateCheckedTodoItem`, bem como da chamada para `SyncAsync` no método `ButtonRefresh_Click`.

10. No método `SyncAsync`, adicione os seguintes manipuladores de exceção:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
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
                  "you can try your pull again when connected with your backend.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

	Em uma situação offline, [PullAsync][PullAsync] pode resultar em uma [MobileServicePushFailedException][MobileServicePushFailedException] com um [PushResult.Status][Status] de [CancelledByNetworkError][CancelledByNetworkError]. Isso acontece quando um push implícito tenta enviar atualizações por push antes do pull pendentes e uma falha ocorre. Para saber mais, consulte [Sincronização de Dados Offline nos Aplicativos Móveis do Azure].

11. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo funciona da mesma forma que antes de habilitar a sincronização offline. No entanto, o banco de dados local é agora populado com dados que podem ser usados em um cenário offline. Em seguida, você vai criar um cenário offline e usar dados armazenados localmente para iniciar o aplicativo.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar o back-end

Nesta seção, você interrompe a conexão com seu Aplicativo Móvel de back-end para simular uma situação offline. Quando você adicionar itens de dados, o manipulador de exceção informará que o aplicativo está operando no modo offline. Nesse estado, novos itens são adicionados ao repositório local e serão sincronizados com o back-end do aplicativo móvel da próxima vez que o envio por push for executado em um estado conectado.

1. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione as seguintes linhas, que usam uma URL de aplicativo móvel inválido:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Observe que quando seu aplicativo também estiver usando a autenticação, isso causará a falha da entrada. Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celulares no dispositivo ou usando o modo avião.

2. Pressione **F5** para compilar e executar o aplicativo. Observe a falha de sincronização na atualização, quando o aplicativo é iniciado.
 
3. Insira os novos itens e observe como o envio por push falha com um status [CancelledByNetworkError] sempre que você clica em **Salvar**. Contudo, os novos itens de tarefas pendentes existem apenas no repositório local até serem enviados por push para o back-end do aplicativo móvel.

	Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comportará como se ainda estivesse conectado ao back-end do aplicativo móvel.

4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

5. (Opcional) No Visual Studio, abra o **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de Dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foram alterados.

6. (Opcional) Use uma ferramenta REST, como o Fiddler ou Postman, para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualize o aplicativo para reconectar o back-end do Aplicativo Móvel

Nesta seção, você reconecta o aplicativo ao back-end do aplicativo móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o back-end do aplicativo móvel. Quando você executar o aplicativo pela primeira vez, o manipulador de eventos de `OnNavigatedTo` chamará `InitLocalStoreAsync`. Isso, por sua vez, chamará `SyncAsync` para sincronizar seu armazenamento local com o banco de dados de back-end. Então, o aplicativo tentará sincronizar na inicialização.

1. Abra o App.xaml.cs no projeto compartilhado. Remova o comentário de sua inicialização anterior do `MobileServiceClient` para usar as URLs corretas do aplicativo móvel e do gateway.

2. Pressione a tecla **F5** para recompilar e executar o projeto. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull assim que o manipulador de eventos de `OnNavigatedTo` é executado.

3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.

4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

  `UpdateCheckedTodoItem` chama `SyncAsync` para sincronizar cada item concluído com o back-end do aplicativo móvel. `SyncAsync` chama push e pull. No entanto, você deve observar que, **sempre que executar um pull em comparação a uma tabela em que o cliente fez alterações, um push no contexto de sincronização do cliente sempre será executado primeiro automaticamente**. Isso serve para garantir que todas as tabelas no repositório local, juntamente com as relações, permaneçam consistentes. Então nesse caso, poderíamos ter removido a chamada a `PushAsync` porque ela é executada automaticamente ao executar um pull. Esse comportamento pode resultar em um push inesperado se você não estiver ciente dele. Para obter mais informações sobre esse comportamento, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].


##Resumo da API

Para dar suporte aos recursos offline dos serviços móveis, usamos a interface [IMobileServiceSyncTable] e inicializamos [MobileServiceClient.SyncContext][synccontext] com um banco de dados SQLite local. Quanto estiver offline, as operações CRUD normais nos aplicativos móveis funcionam como se o aplicativo ainda estivesse conectado, porém as operações ocorrem no repositório local. Os métodos a seguir são usados para sincronizar o repositório local com o servidor:

*  **[PushAsync]** Como esse método é um membro de [IMobileServicesSyncContext], alterações em todas as tabelas são enviadas por push para o back-end. Somente os registros com as alterações locais são enviados para o servidor.

* **[PullAsync]** Um pull é iniciado de uma [IMobileServiceSyncTable]. Quando houver alterações controladas na tabela, um envio por push implícito é executado para certificar-se de que todas as tabelas no repositório local, bem como suas relações, permaneçam consistentes. O parâmetro *pushOtherTables* controla se outras tabelas no contexto são enviadas por push em um push implícito. O parâmetro *query* aceita uma cadeia de caracteres de consulta [IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] ou OData para filtrar os dados retornados. O parâmetro *queryId* é usado para definir a sincronização incremental. Para saber mais, consulte [Sincronização de Dados Offline nos Aplicativos Móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Seu aplicativo deve periodicamente chamar esse método para limpar os dados obsoletos do repositório local. Use o parâmetro *force* quando for necessário limpar as alterações que ainda não foram sincronizadas.

Para obter mais informações sobre esses conceitos, consulte [Sincronização de Dados Offline nos Aplicativos Móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## Mais informações

Os tópicos a seguir fornecem informações básicas adicionais sobre o recurso de sincronização offline dos Aplicativos Móveis:

* [Sincronização de dados offline em Aplicativos Móveis do Azure]
* [Cobertura em Nuvem: sincronização offline nos Serviços Móveis do Azure] (Observe que o vídeo destina-se aos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)
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
[Criar um aplicativo Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[Criar um aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[Cobertura em Nuvem: sincronização offline nos Serviços Móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0518_2016-->