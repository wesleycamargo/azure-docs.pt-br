Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  Se você ainda não tiver o [SDK dos Serviços Móveis Android][SDK dos Serviços Móveis Android], baixe-o agora e expanda os arquivos compactados.

2.  Copie os arquivos `.jar` da pasta `mobileservices` do SDK para a pasta `libs` do projeto GetStartedWithData.

3.  No Explorer de Pacotes no Eclipse, clique na pasta `libs`, clique em **Atualizar** e os arquivos jar copiados serão exibidos

    Isso adiciona a referência ao SDK dos Serviços Móveis ao espaço de trabalho.

4.  Abra o arquivo AndroidManifest.xml e adicione a linha a seguir, que permite que o aplicativo acesse os Serviços Móveis no Azure.

        <uses-permission android:name="android.permission.INTERNET" />

5.  No Explorador de Pacotes, abra o arquivo TodoActivity.java localizado no pacote com.example.getstartedwithdata e remova os comentários das seguintes linhas de código:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Removeremos a lista na memória usada atualmente pelo aplicativo, portanto, é possível substituí-la por um serviço móvel. Na classe **ToDoActivity**, comente a seguinte linha de código que define a lista **toDoItemList** existente.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Salve o arquivo e o projeto indicará erros de compilação. Pesquise os três locais restantes onde a variável `toDoItemList` é usada e comente as seções indicadas. Remova também o `import java.util.ArrayList`. Isso remove totalmente a lista na memória.

8.  Agora adicionamos o nosso serviço móvel. Remova os comentários das seguintes linhas de código:

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Localize a classe ProgressFilter na parte inferior do arquivo e remova os comentários dela. Essa classe exibe um indicador 'loading' enquanto o MobileServiceClient está executando operações de rede.

10. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

11. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][0]

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

    Isso criará uma nova instância de MobileServiceClient que é usada para acessar seu serviço móvel. Também cria a instância MobileServiceTable que é usada para armazenamento de dados de proxy no serviço móvel.

13. No código acima, substitua `MobileServiceUrl` e `AppKey` pela URL e a chave do aplicativo de seu serviço móvel, nessa ordem.

14. Remova os comentários destas linhas do método **checkItem**:

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    Isso envia uma atualização de item ao serviço móvel e remove os itens selecionados do adaptador.

15. Remova os comentários destas linhas do método **addItem**:

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Esse código cria um novo item e o insere na tabela no serviço móvel remoto.

16. Remova os comentários destas linhas do método **refreshItemsFromTable**:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    Isso consulta o serviço móvel e retorna todos os itens que não estão marcados como concluídos. Os itens são adicionados ao adaptador para vinculação.



  [SDK dos Serviços Móveis Android]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/download-android-sample-code/mobile-dashboard-tab.png
