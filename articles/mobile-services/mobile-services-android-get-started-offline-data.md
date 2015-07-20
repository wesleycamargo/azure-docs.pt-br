<properties
	pageTitle="Adicione a sincronização de dados Offline para seu aplicativo de serviços móveis Android | Mobile Dev Center"
	description="Saiba como usar os serviços móveis do Azure para cache e sincronização de dados offline no seu aplicativo Android"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="ricksal"/>

# Adicionar a sincronização de dados Offline para seu aplicativo de serviços móveis Android

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## Resumo

Os aplicativos móveis podem perder a conectividade de rede ao mudar para uma área sem serviço ou devido a problemas de rede. Por exemplo, um aplicativo do setor de construção usado em um site remoto construção pode precisar inserir dados de agendamento que estão sincronizados até o Azure posteriormente. Com a sincronização offline de serviços móveis do Azure, você pode continuar trabalhando enquanto a conectividade de rede for perdida, o que é essencial para muitos aplicativos móveis. Com a sincronização offline, trabalhar com uma cópia local de sua tabela de servidor do SQL Azure e ressincroniza periodicamente as duas.

Neste tutorial, você irá atualizar o aplicativo a partir de [Tutorial de Início Rápido dos Serviços Móveis] para habilitar a sincronização offline e, em seguida, testar o aplicativo adicionando dados off-line, sincronizar esses itens no banco de dados online e verificar as alterações no Portal de gerenciamento do Azure.

Se você estiver offline conectado ou não, conflitos podem ocorrer sempre que várias alterações forem feitas nos dados. Um tutorial futuro explorará tratamento de conflitos de sincronização, onde você pode escolher qual versão das alterações deve aceitar. Neste tutorial, estamos supondo que não existe nenhum conflito de sincronização e as alterações feitas aos dados existentes serão aplicadas diretamente ao servidor do SQL Azure.


## Para começar, você precisa do seguinte:

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]


## Atualizar o aplicativo para dar suporte à sincronização offline

Com sincronização offline você lê e grava a partir uma *tabela de sincronização* (usando a interface *IMobileServiceSyncTable*), que é parte de um banco de dados **SQL Light**no dispositivo.

Para enviar e receber alterações entre o dispositivo e os serviços móveis do Azure, você deve usar um *o contexto de sincronização* (*MobileServiceClient.SyncContext*), que você inicializar com o banco de dados local para armazenar dados localmente.

1. Adicione permissão para verificar se há conectividade de rede, adicionando este código para o arquivo *Androidmanifest*:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Adicione as seguintes instruções de **importação** a *ToDoActivity.java*:

		import java.util.Map;
		
		import android.widget.Toast;
		
		import com.microsoft.windowsazure.mobileservices.table.query.Query; 
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext; 
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable; 
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType; 
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore; 

3. Na parte superior da classe `ToDoActivity`, altere a declaração da variável `mToDoTable` de uma classe `MobileServiceTable<ToDoItem>` para uma classe `MobileServiceSyncTable<ToDoItem>`.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	Isso é onde você define a tabela de sincronização.

4. Para lidar com a inicialização do armazenamento local, no método `onCreate`, adicione as linhas a seguir depois de criar a instância `MobileServiceClient`:

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. Após o código anterior, que está dentro de um bloco `try`, adicione mais o bloco `catch` seguindo o `MalformedURLException`:

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Adicione este método para verificar a conectividade de rede:

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Adicione este método para sincronização entre o armazenamento de *SQL Light* local e o servidor do SQL Azure:

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {
	
					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. No método `onCreate`, adicione este código como a próxima à última linha, um pouco antes da chamada a `refreshItemsFromTable`:

			syncAsync();

	Isso faz com que o dispositivo na inicialização para sincronizar com a tabela do Azure. Caso contrário, exibirá o último conteúdo offline do repositório local.


 
9. Atualize o código no método `refreshItemsFromTable` para usar esta consulta (primeira linha de código dentro do bloco `try`):

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. No método `onOptionsItemSelected`, substitua o conteúdo do bloco `if` por este código:

			syncAsync();
			refreshItemsFromTable();

	Esse código é executado quando você pressiona o botão **atualizar** no canto superior direito. Isso é o principal meio de sincronizar seu repositório local no Azure, além de sincronizar na inicialização. Isso incentiva o envio em lote de alterações de sincronização e é eficiente, considerando que a recepção do Azure é uma operação relativamente cara. Também é possível criar esse aplicativo para sincronizar cada alteração, adicionando uma chamada para os métodos `syncAsync` para o `addItem` e `checkItem`, se seu aplicativo precisar disso.


## Testar o aplicativo

Nesta seção, você testará o comportamento com WiFi ativo e, depois, com ele desativo para criar um cenário offline.

Quando você adiciona itens de dados, eles são mantidos no armazenamento local do SQL Light , mas não sincronizados para o serviço móvel, até que você pressione o botão **Atualizar** botão. Outros aplicativos podem ter necessidades diferentes em relação a quando os dados precisam ser sincronizados, mas para fins de demonstração neste tutorial o usuário tem que solicitá-lo explicitamente.

Quando você pressiona o botão, uma nova tarefa em segundo plano é iniciada e envia primeiro todas as alterações feitas no armazenamento local, usando o contexto de sincronização e, em seguida, recebe todos os dados alterados do Azure para a tabela local.


### Teste online

Vamos testar os cenários a seguir.

1. Adicione alguns novos itens ao seu dispositivo; 
2. Certifique-se de que os itens não aparecem no portal; 
3. a seguir, pressione **Atualizar** e certifique-se de que eles sejam exibidos.
4. Altere ou adicione um item no portal, pressione **Atualizar** e certifique-se de que as alterações apareceram em seu dispositivo.

### Teste offline

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Coloque o dispositivo ou o simulador em *Modo de Avião*. Isso cria um cenário offline.

2. Adicione alguns itens *ToDo* ou marque alguns itens como concluídos. Feche o dispositivo ou simulador (ou feche forçadamente o aplicativo) e reinicie. Certifique-se de que as alterações foram mantidas no dispositivo porque elas ainda estão vigentes no armazenamento do SQL Light local.

3. Exiba o conteúdo da tabela *TodoItem* do Azure. Confirme que os novos itens _não_ tenham sido sincronizados com o servidor:

   - Para o back-end do JavaScript, vá até o Portal de Gerenciamento e clique na guia Dados para exibir o conteúdo da tabela `TodoItem`.
   - Para o back-end do .NET, exiba o conteúdo da tabela com uma ferramenta SQL como o *SQL Server Management Studio*, ou então com um cliente REST, como o *Fiddler* ou *Postman*.

4. Ative o WiFi no simulador ou dispositivo. A seguir, pressione o botão **Atualizar**.

5. Exiba os dados de TodoItem novamente no portal do Azure. Os TodoItems novos e modificados agora devem aparecer.


## Próximas etapas

* [Usando a exclusão reversível nos Serviços Móveis][Soft Delete]

## Recursos adicionais

* [Cloud Cover: sincronização offline em serviços móveis do Azure]

* [Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure] (observação: demonstrações são para Windows, mas a discussão dos recursos se aplica a todas as plataformas)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Get started with data]: mobile-services-android-get-started-data.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Cloud Cover: sincronização offline em serviços móveis do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicativos habilitados para uso offline nos Serviços Móveis do Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Tutorial de Início Rápido dos Serviços Móveis]: mobile-services-android-get-started.md

<!---HONumber=July15_HO2-->