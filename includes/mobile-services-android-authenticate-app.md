1.  No Gerenciador de Pacotes no Eclipse, abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Adicione o método a seguir à classe **ToDoActivity**:

        private void authenticate() {

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {

                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {

                            if (exception == null) {
                                createAndShowDialog(String.format(
                                                "You are now logged in - %1$2s",
                                                user.getUserId()), "Success");
                                createTable();
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }

    Isso cria um novo método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Google. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Se voc&ecirc; estiver usando um provedor de identidade diferente do Google, altere o valor passado para o m&eacute;todo <strong>logon</strong> acima para um dos seguintes: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em>, ou <em>windowsazureactivedirectory</em>.</p>
</div>

3.  No método **onCreate**, adicione a linha de código a seguir após o código que cria uma instância do objeto `MobileServiceClient`.

        authenticate();

    Essa chamada inicia o processo de autenticação.

4.  Mova o código restante após `authenticate();` no método **onCreate** para um novo método **createTable** parecido com o seguinte:

        private void createTable() {

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }

5.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido.

    Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.


