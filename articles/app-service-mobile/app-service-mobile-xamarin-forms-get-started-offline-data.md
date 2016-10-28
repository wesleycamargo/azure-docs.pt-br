<properties
    pageTitle="Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin.Forms) | Microsoft Azure"
    description="Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo Xamarin.Forms"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="06/18/2016"
    ms.author="glenga"/>

# Habilitar sincronização offline para seu aplicativo móvel Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral
Este tutorial apresenta o recurso de sincronização offline de Aplicativos móveis do Azure para Xamarin.Forms. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações são sincronizadas ao serviço remoto.

Este tutorial se baseia na solução de início rápido do Xamarin.Forms para aplicativos móveis criados quando você conclui o tutorial [Criar um aplicativo iOS Xamarin]. A solução de início rápido para Xamarin.Forms contém o código para dar suporte à sincronização offline, que só precisa ser habilitada. Neste tutorial, você atualizará a solução de início rápido para ativar os recursos offline dos Aplicativos Móveis do Azure. Também destacaremos o código especificamente offline no aplicativo. Se você não usar a solução de início rápido baixada, deverá adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## Habilitar a funcionalidade de sincronização offline na solução de início rápido

O código de sincronização offline é incluído no projeto usando diretivas de pré-processador C#. Quando o símbolo **OFFLINE\_SYNC\_ENABLED** é definido, esses caminhos de código são incluídos na compilação. Para aplicativos do Windows, você deve instalar também a plataforma SQLite.

1. No Visual Studio, clique com o botão direito do mouse na solução > **Gerenciar Pacotes NuGet para a Solução...**, procure e instale o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos na solução.

2. No Gerenciador de Soluções, abra o arquivo TodoItemManager.cs do projeto com **Portable** no nome, que é o projeto da Biblioteca de Classes Portátil e remova o comentário da seguinte diretiva de pré-processador:

		#define OFFLINE_SYNC_ENABLED

3. No Gerenciador de Soluções, abra o arquivo TodoList.xaml.cs do projeto **Portable**, localize o método **OnAppearing** e certifique-se de que `true` é passado para *syncItems* ao chamar **RefreshItems**, da seguinte maneira:

		await RefreshItems(true, syncItems: true);
	Isso significa que o aplicativo tentará sincronizar com o back-end quando for iniciado.
 
4. (Opcional) Se você estiver dando suporte a dispositivos iOS, abra o arquivo AppDelegate.cs do projeto **iOS** e remova a seguinte linha de código no método **FinishedLaunching**:

		SQLitePCL.CurrentPlatform.Init();

	Isso inicializa o armazenamento SQLite em dispositivos iOS. As tarefas restantes só serão necessárias para oferecer suporte a dispositivos Windows.

5. (Opcional) Para dar suporte a dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

	* **Tempo de Execução do Windows 8.1:** instalar o [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716919).
    * **Windows Phone 8.1:** instalar o [SQLite para Windows Phone 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716920).
    * **Plataforma Universal do Windows** Instale o [SQLite para a Plataforma Universal do Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix). Atualmente, o início rápido não inclui um projeto da Plataforma Universal do Windows (UWP).

6. (Opcional) Em cada projeto de aplicativo do Windows, clique com botão direito do mouse em **Referências** > **Adicionar Referência …**, expanda a pasta **Windows** > **Extensões**, habilite o SDK do **SQLite para Tempo de Execução do Windows** apropriado junto com o SDK do **Tempo de Execução do Visual C++ 2013 para Windows**. Observe que os nomes do SDK do SQLite variam ligeiramente de acordo com cada plataforma Windows. Para um projeto UWP, instale o SDK do **Tempo de Execução do Visual C++ 2015 para aplicativos da Plataforma Universal do Windows**.


## Examine o código de sincronização do cliente

Aqui há uma breve visão geral do que já está incluído no código do tutorial nas diretivas do `#if OFFLINE_SYNC_ENABLED`. Observe que a funcionalidade de sincronização offline está no arquivo de projeto TodoItemManager.cs no projeto da Biblioteca de Classes Portátil. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados offline nos Aplicativos Móveis do Azure].

* Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. O banco de dados de armazenamento local é inicializado no construtor da classe **TodoItemManager** usando o seguinte código:

	    var store = new MobileServiceSQLiteStore("localstore.db");
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

	Isso cria um novo banco de dados SQLite local usando a classe **MobileServiceSQLiteStore**. O método **DefineTable** cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem`, neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

* O campo **todoTable** no **TodoItemManager** é um tipo **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Essa classe usa o banco de dados local para todas as operações de criação, leitura, atualização e exclusão (CRUD) de tabela. Você decide quando essas alterações são enviadas por push para o back-end do Aplicativo Móvel ao chamar **PushAsync** no **IMobileServiceSyncContext** usado pelo cliente. Esse contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o **PushAsync** é chamado.

	O método **SyncAsync** a seguir é chamado para sincronização com o back-end do Aplicativo Móvel:

		public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling. 
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
						error.TableName, error.Item["id"]);
                }
            }
        }

	Este exemplo usa o tratamento de erros simples com o manipulador de sincronização padrão. Um aplicativo real trataria os diversos erros como condições de rede, conflitos de servidor e outros usando uma personalização da implementação de **IMobileServiceSyncHandler**.

##Considerações sobre a sincronização offline

No exemplo, o método **SyncAsync** é chamado somente na inicialização e quando a sincronização é especificamente solicitada. Para iniciar a sincronização em um aplicativo Android ou iOS, puxe para baixo a lista de itens; para o Windows, use o botão **Sincronizar**. Em um aplicativo real, você também poderia disparar essa funcionalidade de sincronização quando o estado da rede fosse alterado.

Quando um pull é executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, e essa operação de pull automaticamente disparará um envio por push de contexto anterior. Durante a atualização, a adição e a conclusão de itens neste exemplo, você pode omitir a chamada explícita a **PushAsync**, pois ela é redundante.

No código fornecido, todos os registros na tabela remota TodoItem são solicitados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta ao **PushAsync**. Para obter mais informações, confira a seção *Sincronização incremental* em [Sincronização de dados offline nos Aplicativos Móveis do Azure].


## Execute o aplicativo cliente

Com a sincronização offline habilitada, agora você pode executar o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do repositório local. Posteriormente, você simulará um cenário offline e modificará os dados no armazenamento local enquanto o aplicativo estiver offline.

## Atualizar o comportamento de sincronização do aplicativo cliente

Nesta seção, você modificará o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Quando você adicionar ou alterar itens de dados, essas alterações serão mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão for restabelecida.

2. No Gerenciador de Soluções, abra o arquivo de projeto Constants.cs do projeto **Portable** e altere o valor de `ApplicationURL` para apontar para uma URL inválida, como a seguir:

        publis static string ApplicationURL = @"https://your-service.azurewebsites.xxx/";


2. Abra o arquivo TodoItemManager.cs do projeto **Portable** e adicione mais um **catch** à classe base **Exception** para o bloco **try... catch** em **SyncAsync**. Esse bloco **catch** grava a mensagem de exceção no console, da seguinte maneira:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Compile e execute o aplicativo cliente, adicione alguns novos itens e observe a exceção registrada no console para cada tentativa de sincronizar com o back-end. Esses novos itens existem apenas no repositório local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar e excluir).

4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

5. (Opcional) Use o Visual Studio para exibir a tabela de Banco de Dados SQL do Azure para ver se os dados no banco de dados do back-end não foram alterados.

	No Visual Studio, abra **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Gerenciador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo.


## Atualize o aplicativo cliente para reconectar seu back-end móvel

Nesta seção, você vai reconectar o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você executa o gesto de atualização, os dados serão sincronizados com o serviço móvel.

1. Reabra Constants.cs e corrija o `applicationURL` para apontar para a URL correta.

2. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após iniciar. Verifique se nenhuma exceção está registrada no console de depuração.

3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end e o repositório local.

    Observe que os dados foram sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] \(observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo iOS Xamarin]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de dados offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0629_2016-->