
1. No **Gerenciador de projetos** , no Android Studio, abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Adicione o método a seguir à classe **ToDoActivity** :

        private void authenticate() {
            // Login using the Google provider.

            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();    
                }
            });       
        }

    Isso cria um novo método para manipular o processo de autenticação. O usuário é autenticado usando uma credencial do Google. Um diálogo exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    > [!NOTE] Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado ao método **login** acima para um destes: _MicrosoftAccount_, _Facebook_, _Twitter_ ou _windowsazureactivedirectory_.

3. No método **OnCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

        authenticate();

    Essa chamada inicia o processo de autenticação.
4. Mova o código restante após `authenticate();` no método **OnCreate** para um novo método **CreateTable**. Isso se parece com o seguinte:

        private void createTable() {

            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from Azure.
            refreshItemsFromTable();
        }
5. No menu **Executar**, clique em **Executar aplicativo** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

Ao entrar com êxito, o aplicativo deve ser executado sem erros e você deve ser capaz de consultar o serviço de back-end e fazer atualizações nos dados.


<!--HONumber=Dec16_HO2-->


