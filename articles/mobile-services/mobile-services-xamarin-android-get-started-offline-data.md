<properties
	pageTitle="Usando dados offline nos serviços móveis (Xamarin Android) | Centro de desenvolvimento de Serviços Móveis"
	description="Saiba como usar serviços móveis do Azure para cache e sincronização de dados offline no seu aplicativo Android Xamarin"
	documentationCenter="xamarin"
	authors="lindydonna"
	editor="wesmc"
	manager="dwrede"
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/20/2015"
	ms.author="donnam"/>

# Usando sincronização de dados offline em Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Este tópico apresenta os recursos de sincronização offline de Serviços Móveis do Azure no aplicativo de início rápido de lista de tarefas pendentes. A sincronização offline permite que você crie facilmente aplicativos que são utilizáveis mesmo quando o usuário final não tem acesso à rede.

A sincronização offline possui diversos usos possíveis:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Torna os aplicativos resilientes em relação à conectividade da rede intermitente
* Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
* Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos

>[AZURE.NOTE]Para concluir este tutorial, será necessária uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação Gratuita do Azure</a>.
>
> Se esta for sua primeira experiência com os Serviços Móveis, você deverá concluir a [Introdução aos Serviços Móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Examinar o código de sincronização de Serviços Móveis]
2. [Atualizar o comportamento de sincronização do aplicativo]
3. [Atualizar o aplicativo para reconectar seu serviço móvel]

Este tutorial exige o seguinte:

* Visual Studio com a [extensão Xamarin] **ou** [Xamarin Studio]
* Conclusão do tutorial [Introdução aos Serviços Móveis]

## <a name="review-offline"></a>Examinar o código de sincronização dos Serviços Móveis

A Sincronização offline dos Serviços móveis do Azure permite aos usuários finais interagir com um banco de dados local quando a rede não está acessível. Para usar esses recursos em seu aplicativo, inicialize `MobileServiceClient.SyncContext` para um armazenamento local. Em seguida, faça referência à sua tabela por meio da interface da `IMobileServiceSyncTable`. Esta seção orienta você quanto ao código relacionado à sincronização offline em `ToDoActivity.cs`.

1. No Visual Studio ou no Xamarin Studio, abra o projeto que você concluiu no tutorial [Introdução aos Serviços Móveis]. Abra o arquivo `ToDoActivity.cs`.

2. Observe que o tipo do membro `toDoTable` é `IMobileServiceSyncTable`. A sincronização offline usa essa interface de tabela de sincronização em vez de `IMobileServiceTable`. Quando uma tabela de sincronização é usada, todas as operações vão para o armazenamento local e só são sincronizadas com o serviço remoto com operações de push e pull explícitas.

    Para obter uma referência a uma tabela de sincronização, utiliza-se o método `GetSyncTable()`. Para remover a funcionalidade de sincronização offline, use `GetTable()`.

3. Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. Isso é feito com o método `InitLocalStoreAsync`:

        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Isso cria um repositório local usando a classe `MobileServiceSQLiteStore`, fornecida no SDK dos Serviços Móveis. Você também pode fornecer uma implementação de repositório local diferente implementando `IMobileServiceLocalStore`.

    O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem`, neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto - é possível armazenar apenas um subconjunto de colunas.

    Essa sobrecarga de `InitializeAsync` usa o manipulador de conflitos padrão, que falha sempre que há um conflito. Para fornecer um manipulador de conflitos personalizado, consulte o tutorial [Tratando conflitos com o suporte offline para os Serviços Móveis].

4. O método `SyncAsync` aciona a operação de sincronização real:

        private async Task SyncAsync()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
        }

    Primeiro, há uma chamada para `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas. Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.

    Em seguida, o método chama `IMobileServiceSyncTable.PullAsync()` para extrair dados de uma tabela no servidor para o aplicativo. Observe que se houver quaisquer alterações pendentes no contexto de sincronização, um pull sempre emite um push primeiro. Isso serve para garantir que todas as tabelas no repositório local, juntamente com os relacionamentos, permaneçam consistentes. Neste caso, chamamos push explicitamente.

    Neste exemplo, recuperamos todos os registros na tabela remota `TodoItem`, mas também é possível filtrar os registros passando uma consulta. O primeiro parâmetro para `PullAsync()` é uma ID de consulta que é usada para sincronização incremental, que usa o carimbo de data/hora `UpdatedAt` para obter apenas os registros modificados desde a última sincronização. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `null` como a ID da consulta. Isso recuperará todos os registros de cada operação de pull, o que é potencialmente ineficiente.

    >[AZURE.NOTE]Para remover registros de armazenamento local do dispositivo quando eles tiverem sido excluídos do banco de dados do serviço móvel, você deve habilitar a [Exclusão Reversível]. Caso contrário, seu aplicativo deverá chamar `IMobileServiceSyncTable.PurgeAsync()` periodicamente para limpar o armazenamento local.

    Observe que a `MobileServicePushFailedException` pode ocorrer tanto por uma operação de push quanto de pull. O próximo tutorial, [Tratando conflitos com suporte offline para Serviços Móveis], mostra como tratar essas exceções relacionadas à sincronização.

5. Na classe `ToDoActivity`, o método `SyncAsync()` é chamado após as operações que modificam dados, `AddItem()` e `CheckItem()`. Ele também é chamado por `OnRefreshItemsSelected()`, de modo que o usuário obtém os dados mais recentes sempre que pressionar o botão **Atualizar**. O aplicativo também realiza uma sincronização na inicialização, já que `ToDoActivity.OnCreate()` chama `OnRefreshItemsSelected()`.

    Como `SyncAsync()` é chamado sempre que dados são modificados, esse aplicativo presume que o usuário está online sempre que este usuário está editando dados. Na próxima seção, atualizaremos o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## <a name="update-sync"></a>Atualizar o comportamento de sincronização do aplicativo

Nesta seção, você modificará o aplicativo para ele não sincronizar na sua inicialização ou nas operações insert e update, mas somente quando o botão Atualizar for pressionado. Em seguida, você interromperá a conexão do aplicativo com o serviço móvel para simular um cenário offline. Quando você adicionar itens de dados, eles serão mantidos no armazenamento local, mas não serão imediatamente sincronizados com o serviço móvel.

1. Na classe `ToDoActivity`, edite os métodos `AddItem()` e `CheckItem()` para comentar as chamadas para `SyncAsync()`.

2. Em `ToDoActivity`, comente as definições dos membros `applicationURL` e `applicationKey`. Adicione as linhas a seguir, que fazem referência a uma URL de serviço móvel inválida:

        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";

3. Em `ToDoActivity.OnCreate()`, remova a chamada a `OnRefreshItemsSelected()` e substitua-a por:

        // Load the items from the Mobile Service
        // OnRefreshItemsSelected (); // don't sync on app launch
        await RefreshItemsFromTableAsync(); // load UI only

4. Compile e execute o aplicativo. Adicione alguns novos itens ToDo. Esses novos itens existem apenas no armazenamento local, até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao serviço móvel, oferecendo suporte a todas as operações CRUD (criação, leitura, atualização, exclusão).

5. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

## <a name="update-online-app"></a>Atualizar o aplicativo para reconectar seu serviço móvel

Nesta seção você reconectará o aplicativo ao serviço móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o serviço móvel. Quando você pressionar o botão **Atualizar**, dados serão sincronizados ao seu serviço móvel.

1. Abra `ToDoActivity.cs`. Remova a URL de serviço móvel inválida e adicione novamente a chave correta de aplicativo e URL.

2. Recompile e execute o aplicativo. Observe que os dados têm a mesma aparência do cenário offline mesmo que agora o aplicativo esteja conectado ao serviço móvel. Isso ocorre porque esse aplicativo sempre usa a `IMobileServiceSyncTable`, que está apontada para o repositório local.

3. Faça logon no Portal de Gerenciamento do Microsoft Azure e observe o banco de dados para seu serviço móvel. Se o serviço usar o back-end do JavaScript, você poderá procurar os dados na guia **Dados** do serviço móvel.

    Se você estiver usando o back-end do .NET para o serviço móvel, no Visual Studio, vá para **Gerenciador de Servidores** -> **Azure** -> **Bancos de Dados SQL**. Clique com o botão direito do mouse em seu Banco de Dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**.

    Observe que os dados *não* foram sincronizados entre o banco de dados e o repositório local.

4. No aplicativo, pressione o botão de atualização. Isso chama `OnRefreshItemsSelected()` que, por sua vez, chama `SyncAsync()`. Isso executará as operações de push e pull, primeiro enviando os itens do armazenamento local para o serviço móvel e, em seguida, recuperando novos dados do serviço.

5. Verifique o banco de dados para o serviço móvel a fim de confirmar que as alterações foram sincronizadas.

##Resumo

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Próximas etapas

* [Tratando conflitos com o suporte offline para Serviços Móveis]

* [Como usar a biblioteca de cliente Componente Xamarin para os Serviços Móveis do Azure]

<!-- Anchors. -->
[Examinar o código de sincronização de Serviços Móveis]: #review-offline
[Atualizar o comportamento de sincronização do aplicativo]: #update-sync
[Atualizar o aplicativo para reconectar seu serviço móvel]: #update-online-app

<!-- Images -->


<!-- URLs. -->
[Tratando conflitos com o suporte offline para Serviços Móveis]: ../mobile-services-xamarin-android-handling-conflicts-offline-data.md
[Tratando conflitos com o suporte offline para os Serviços Móveis]: ../mobile-services-xamarin-android-handling-conflicts-offline-data.md
[Tratando conflitos com suporte offline para Serviços Móveis]: ../mobile-services-xamarin-android-handling-conflicts-offline-data.md
[Get started with data]: mobile-services-android-get-started-data.md
[Introdução aos Serviços Móveis]: mobile-services-android-get-started.md
[Como usar a biblioteca de cliente Componente Xamarin para os Serviços Móveis do Azure]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Exclusão Reversível]: mobile-services-using-soft-delete.md

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Xamarin Studio]: http://xamarin.com/download
[extensão Xamarin]: http://xamarin.com/visual-studio
[NuGet Addin for Xamarin]: https://github.com/mrward/monodevelop-nuget-addin

<!--HONumber=54--> 