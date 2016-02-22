<properties
	pageTitle="Habilitar sincronização offline para o seu Aplicativo móvel do Azure (Android)"
	description="Aprenda a usar os aplicativos móveis do Serviço de Aplicativo para cache e sincronização de dados offline no aplicativo Android."
	documentationCenter="android"
	authors="lindydonna"
	manager="dwrede"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="donnam"/>

# Habilitar sincronização offline para seu aplicativo móvel Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Visão geral

Este tutorial aborda o recurso de sincronização offline de Aplicativos móveis do Azure para Android. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local; quando o dispositivo estiver online novamente, essas alterações serão sincronizadas ao serviço remoto.

Se essa for sua primeira experiência com os Aplicativos Móveis do Azure, você deve primeiro concluir o tutorial [Criar um aplicativo do iOS]. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de dados offline nos Aplicativos Móveis do Azure].

## Atualizar o aplicativo para dar suporte à sincronização offline

Com sincronização offline você lê e grava de uma *tabela de sincronização* (usando a interface *IMobileServiceSyncTable*), que é parte de um banco de dados **SQLite** no dispositivo.

Para enviar e receber alterações entre o dispositivo e os serviços móveis do Azure, você deve usar um *o contexto de sincronização* (*MobileServiceClient.SyncContext*), que você inicializar com o banco de dados local para armazenar dados localmente.

1. Em `TodoActivity.java`, comente a definição existente de `mToDoTable` e remova a versão da tabela de sincronização:

	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. No método `onCreate`, comente a inicialização existente de `mToDoTable` e remova essa definição:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. Em `refreshItemsFromTable`, comente a definição de `results` e remova essa definição:

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Comente a definição de `refreshItemsFromMobileServiceTable`:

5. Remova a definição de `refreshItemsFromMobileServiceTableSyncTable`:

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. Remova a definição de `sync`:

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## Testar o aplicativo

Nesta seção, você testará o comportamento com WiFi ativo e, depois, com ele desativo para criar um cenário offline.

Quando você adiciona itens de dados, eles são mantidos no armazenamento local do SQL Lite , mas não sincronizados para o serviço móvel, até que você pressione o botão **Atualizar**. Outros aplicativos podem ter necessidades diferentes em relação a quando os dados precisam ser sincronizados, mas para fins de demonstração neste tutorial o usuário tem que solicitá-lo explicitamente.

Quando você pressiona o botão, uma nova tarefa em segundo plano é iniciada e envia primeiro todas as alterações feitas no armazenamento local, usando o contexto de sincronização e, em seguida, recebe todos os dados alterados do Azure para a tabela local.

### Teste offline

1. Coloque o dispositivo ou simulador em *Modo de Avião*. Isso cria um cenário offline.

2. Adicione alguns itens *ToDo* ou marque alguns itens como concluídos. Feche o dispositivo ou simulador (ou feche forçadamente o aplicativo) e reinicie. Por ainda estarem vigentes no armazenamento do SQL Lite local, as alterações ficam mantidas no dispositivo.

3. Visualize os conteúdos da tabela *TodoItem* do Azure com uma ferramenta SQL, como o *SQL Server Management Studio*, ou então com um cliente REST, como o *Fiddler* ou *Postman*. Confirme que os novos itens _não_ foram sincronizados com o servidor

   	+ Para um back-end Node.js, vá para o [Portal do Azure](https://portal.azure.com/) e no back-end do aplicativo móvel clique em **Tabelas fáceis** > **TodoItem** para exibir o conteúdo da tabela `TodoItem`.
   	+ Para um back-end do .NET, visualize o conteúdo da tabela com uma ferramenta SQL, como o *SQL Server Management Studio*, ou então com um cliente REST, como o *Fiddler* ou *Postman*.

4. Ative o WiFi no simulador ou dispositivo. Em seguida, pressione o botão **Atualizar**.

5. Exiba os dados de TodoItem novamente no portal do Azure. Os TodoItems novos e modificados agora devem aparecer.

## Recursos adicionais

* [Sincronização de dados offline em Aplicativos Móveis do Azure]

* [Cobertura em nuvem: sincronização offline nos serviços móveis do Azure] (observação: o vídeo está nos Serviços Móveis, mas a sincronização offline funciona de maneira semelhante nos Aplicativos Móveis do Azure)


<!-- URLs. -->

[Sincronização de dados offline em Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md
[Sincronização de dados offline nos Aplicativos Móveis do Azure]: ../app-service-mobile-offline-data-sync.md

[Criar um aplicativo do iOS]: ../app-service-mobile-android-get-started.md

[Cobertura em nuvem: sincronização offline nos serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0211_2016-->