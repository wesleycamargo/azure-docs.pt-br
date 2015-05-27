<properties
    pageTitle="Habilitar sincronização offline para o seu aplicativo móvel (Xamarin iOS)"
    description="Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo iOS"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/26/2015"
    ms.author="donnam"/>

# Habilitar sincronização offline para seu aplicativo móvel Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../includes/app-service-mobile-selector-offline-preview.md)]

Este tutorial aborda o recurso de sincronização offline de aplicativos móveis para iOS. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações são sincronizadas ao serviço remoto.

A sincronização offline possui diversos usos possíveis:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Torna os aplicativos resilientes em relação à conectividade da rede intermitente
* Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
* Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos

Se esta for sua primeira experiência com aplicativos móveis, primeiro conclua o tutorial [Criar um aplicativo iOS Xamarin].

Este tutorial exige o seguinte:

* Visual Studio 2013
* [Extensão Xamarin] do Visual Studio **ou** [Xamarin Studio em OS X]

## Examinar o código de sincronização do aplicativo móvel

A sincronização offline do aplicativo móvel permite aos usuários finais interagirem com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, você deve inicializar `MobileServiceClient.SyncContext` para um repositório local. Então consulte sua tabela por meio da interface `IMobileServiceSyncTable`. Esta seção orienta você quanto ao código relacionado à sincronização offline em `QSTodoService.cs`.

1. No Visual Studio, abra o projeto que você concluiu no tutorial [Introdução a Aplicativos Móveis]. Abra o arquivo `QSTodoService.cs`.

2. Observe que o tipo do membro `todoTable` é `IMobileServiceSyncTable`. A sincronização offline usa essa interface de tabela de sincronização, em vez de `IMobileServiceTable`. Quando uma tabela de sincronização é usada, todas as operações vão para o armazenamento local e só são sincronizadas com o serviço remoto com operações de push e pull explícitas.

    Para obter uma referência a uma tabela de sincronização, utiliza-se o método `GetSyncTable()`. Para remover a funcionalidade de sincronização offline, use `GetTable()` em vez disso.

3. Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. Isso é feito com o método `InitializeStoreAsync`:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Isso cria um repositório local usando a classe `MobileServiceSQLiteStore`, que é fornecida no SDK do aplicativo móvel. Você também pode fornecer uma implementação de repositório local diferente implementando `IMobileServiceLocalStore`.

    O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, que é `ToDoItem` neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto - é possível armazenar apenas um subconjunto de colunas.

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. O método `SyncAsync` aciona a verdadeira operação de sincronização:

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

    Primeiro, há uma chamada para `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas. Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

    Em seguida, o método chama `IMobileServiceSyncTable.PullAsync()` para extrair dados de uma tabela no servidor para o aplicativo. Observe que se houver quaisquer alterações pendentes no contexto de sincronização, um pull sempre emite um push primeiro. Isso serve para garantir que todas as tabelas no repositório local, juntamente com os relacionamentos, permaneçam consistentes. Neste caso, chamamos push explicitamente.

    Neste exemplo, recuperamos todos os registros na tabela remota `TodoItem`, mas também é possível filtrar os registros realizando uma consulta. O primeiro parâmetro para `PullAsync()` é uma ID de consulta que é usada para sincronização incremental, que usa o carimbo de data/hora `UpdatedAt` para obter apenas os registros modificados desde a última sincronização. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `null` como a ID da consulta. Isso recuperará todos os registros de cada operação de pull, o que é potencialmente ineficiente.

<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `IMobileServiceSyncTable.PurgeAsync()` to purge the local store.

    Note that the `MobileServicePushFailedException` can occur for both a push and a pull operation. The next tutorial, [Handling conflicts with offline support for Mobile Services], shows how to handle these sync related exceptions.
-->

5. Na classe `QSTodoService`, o método `SyncAsync()` é chamado após as operações que modificam dados, `InsertTodoItemAsync()` e `CompleteItemAsync`. Ele também é chamado por meio de `RefreshDataAsync()`, de modo que o usuário obtém os dados mais recentes sempre que executar o gesto de atualização. O aplicativo também realiza uma sincronização na inicialização, já que `QSTodoListViewController.ViewDidLoad()` chama `RefreshDataAsync()`.

    Como `SyncAsync()` é chamado sempre que dados são modificados, esse aplicativo presume que o usuário está online sempre que este usuário está editando dados. Na próxima seção, atualizaremos o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## Atualizar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para ele não sincronizar na sua inicialização ou nas operações insert e update, mas somente quando o gesto de atualização for realizado. Em seguida, você interromperá a conexão do aplicativo com o back-end móvel para simular um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no repositório local, mas não serão imediatamente sincronizados com o repositório de dados do back-end móvel.

1. Abra `QSTodoService.cs`. Comente as chamadas a `SyncAsync()` nos métodos a seguir:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Agora, `RefreshAsync()` só carregará dados do repositório local, mas não se conectará ao back-end do aplicativo.

2. Em `QSTodoService.cs`, altere a definição de `applicationURL` para apontar para um URI de aplicativo móvel inválido:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Para garantir que os dados sejam sincronizados quando o gesto de atualização for executado, edite o método `QSTodoListViewController.RefreshAsync()`. Adicione uma chamada a `SyncAsync()` antes da chamada a `RefreshDataAsync()`:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Compile e execute o aplicativo. Adicione alguns novos itens ToDo. Esses novos itens existem apenas no repositório local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar e excluir).

5. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## Atualizar o aplicativo para reconectar seu back-end móvel

Nesta seção, você vai reconectar o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você executa o gesto de atualização, os dados serão sincronizados com o serviço móvel.

1. Abra `QSTodoService.cs`. Remova a URL inválida do aplicativo móvel e adicione novamente a URL correta e a chave de aplicativo.

2. Recompile e execute o aplicativo. Observe que os dados não foram alterados, embora o aplicativo agora esteja conectado ao back-end móvel. Isso ocorre porque esse aplicativo sempre usa o `IMobileServiceSyncTable`, que está voltado ao repositório local.

3. Conecte-se ao banco de dados SQL do back-end para exibir os dados que foram armazenados. No Visual Studio, vá para **Gerenciador de Servidores** -> **Azure** -> **Bancos de Dados SQL**. Clique com o botão direito do mouse em seu Banco de Dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**.

    Observe que os dados *não* foram sincronizados entre o banco de dados e o repositório local.

4. No aplicativo, faça o gesto de atualização puxando a lista de itens para baixo. Isso faz com que o aplicativo chame `RefreshDataAsync()`, que, por sua vez, chama `SyncAsync()`. Isso executará as operações de envio e recebimento, primeiro enviando os itens do repositório local para o back-end móvel e, em seguida, recuperando novos dados do back-end.

5. Atualize a exibição do banco de dados e confirme que as alterações foram sincronizadas.

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo iOS Xamarin]: app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio em OS X]: http://xamarin.com/download
[Extensão Xamarin]: http://xamarin.com/visual-studio

<!--HONumber=54-->