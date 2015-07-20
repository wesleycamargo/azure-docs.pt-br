Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1. Verificar se você tem as seguintes linhas na marca **dependências** no arquivo *build.gradle (módulo de aplicativo)* e, se não, adicione-as. Isso adiciona as referências para o SDK de Cliente Android dos Serviços Móveis.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2+'


2. Agora, recompile o projeto clicando em **Sincronizar projetos com arquivos do Gradle**.

3. Abra o arquivo AndroidManifest.xml e adicione a linha a seguir, que permite que o aplicativo acesse os Serviços Móveis no Azure.

		<uses-permission android:name="android.permission.INTERNET" />


4. No Gerenciador de Projetos, abra o arquivo TodoActivity.java localizado na pasta **GetStartedWithData => app => src => java** e remova os comentários das seguintes linhas de código:



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. Comente as linhas a seguir:

		import java.util.ArrayList;
		import java.util.List;

6. Removeremos a lista na memória usada atualmente pelo aplicativo, portanto, é possível substituí-la por um serviço móvel. Na classe **ToDoActivity**, comente a seguinte linha de código que define a lista **toDoItemList** existente.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Salve o arquivo e o projeto indicará erros de compilação. Pesquise os três locais restantes onde a variável `toDoItemList` é usada e comente as seções indicadas. Isso remove totalmente a lista na memória.

8. Agora adicionamos o nosso serviço móvel. Remova os comentários das seguintes linhas de código:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Localize a classe *ProgressFilter* na parte inferior do arquivo e remova os comentários dela. Essa classe exibe um indicador 'loading' enquanto o *MobileServiceClient* está executando operações de rede.


10. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

11. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

12. No método **onCreate**, remova os comentários das seguintes linhas de código que definem a variável **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Isso criará uma nova instância de *MobileServiceClient* que é usada para acessar seu serviço móvel. Também cria a instância *MobileServiceTable* que é usada para armazenamento de dados de proxy no serviço móvel.

13. No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL e a chave do aplicativo de seu serviço móvel, nessa ordem.



14. Remova os comentários destas linhas do método **checkItem**:

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	Isso envia uma atualização de item ao serviço móvel e remove os itens selecionados do adaptador.
    
15. Remova os comentários destas linhas do método **addItem**:
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	Esse código cria um novo item e o insere na tabela no serviço móvel remoto.

16. Remova os comentários destas linhas do método **refreshItemsFromTable**:

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	Isso consulta o serviço móvel e retorna todos os itens que não estão marcados como concluídos. Os itens são adicionados ao adaptador para vinculação.
		

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO2-->