<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin iOS)"
    description="Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo iOS"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="02/04/2016"
    ms.author="wesmc"/>

# Habilitar sincronização offline para seu aplicativo móvel Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral

Este tutorial apresenta o recurso de sincronização offline de Aplicativos móveis do Azure para Xamarin.iOS. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações são sincronizadas ao serviço remoto.

Neste tutorial, você atualizará o projeto de aplicativo do Xamarin.iOS de [Criar um aplicativo Xamarin.iOS] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## Examine o código de sincronização do cliente

O projeto de cliente Xamarin que você baixou ao concluir o tutorial [Criar um aplicativo Xamarin iOS] já contém a sincronização offline do código de suporte usando um banco de dados SQLite local. Aqui há um resumo breve do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados Offline em aplicativos móveis do Azure].

* Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado quando o `QSTodoListViewController.ViewDidLoad()` executa o `QSTodoService.InitializeStoreAsync()`. Isso cria um novo banco de dados SQLite local usando a classe `MobileServiceSQLiteStore` fornecida pelo SDK do cliente de Aplicativos Móveis do Azure.

	O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem`, neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

		// QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* O membro `todoTable` do `QSTodoService` é do tipo `IMobileServiceSyncTable` em vez do `IMobileServiceTable`. Isso instrui todas as operações de tabela CRUD (Criar, Ler, Atualizar e Excluir) no banco de dados do armazenamento local.

	Você decide quando essas alterações são enviadas por push para o back-end do aplicativo móvel do Azure chamando o `IMobileServiceSyncContext.PushAsync()` usando o contexto de sincronização para a conexão do cliente. O contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o `PushAsync` é chamado.

	O código fornecido chama o `QSTodoService.SyncAsync()` para sincronizar sempre que a lista todoitem é atualizada ou um todoitem é adicionado ou concluído. Então ele sincroniza após cada alteração local executa um envio por push no contexto de sincronização e um pull na tabela de sincronização. No entanto, é importante observar que se o pull é executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, e essa operação de pull automaticamente disparará um primeiro envio por push de contexto. Para esses casos (itens atualizar, adicionar e concluir), você poderia omitir a chamada explícita `PushAsync`. Ela é redundante.

    No código fornecido, todos os registros na tabela remota `TodoItem` são solicitados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta ao `PushAsync`. Para obter mais informações, confira a seção *Sincronização incremental* em [Sincronização de dados offline nos Aplicativos Móveis do Azure].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
	-->


		// QSTodoService.cs

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## Execute o aplicativo cliente

Execute o aplicativo cliente pelo menos uma vez para preencher o banco de dados do armazenamento local. Na próxima seção, você simulará um cenário offline e modificará os dados no armazenamento local enquanto o aplicativo estiver offline.


## Atualize o comportamento de sincronização do aplicativo cliente

Nesta seção, você modificará o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Quando você adicionar ou alterar itens de dados, essas alterações serão mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão for restabelecida.

1. Na parte superior de `QSTodoService.cs`, altere a inicialização do `applicationURL` para apontar para URLs inválidas:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/";


2. Adicione um `catch` adicional para a classe `Exception` em `QSTodoService.SyncAsync` que gravará a mensagem de exceção no console.

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. Compile e execute o aplicativo cliente. Adicione alguns novos itens de tarefas e observe a exceção registrada no console para cada tentativa de sincronizar com o back-end do aplicativo móvel. Esses novos itens existem apenas no repositório local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar e excluir).

4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

5. (Opcional) Use o Visual Studio para exibir a tabela de Banco de Dados SQL do Azure para ver se os dados no banco de dados do back-end não foram alterados.

	No Visual Studio, abra **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo.


## Atualize o aplicativo cliente para reconectar seu back-end móvel

Nesta seção, você vai reconectar o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você executa o gesto de atualização, os dados serão sincronizados com o serviço móvel.

1. Abra `QSTodoService.cs`. Corrija o `applicationURL` e `gatewayURL` para apontar para as URLs corretas.

2. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel do Azure após iniciar. Verifique se nenhuma exceção está registrada no console de depuração.

3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.

    Observe que os dados foram sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] (observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Criar um aplicativo Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de dados Offline em aplicativos móveis do Azure]: app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0413_2016-->