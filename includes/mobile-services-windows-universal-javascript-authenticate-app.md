

1. Abra o arquivo do projeto default.js e na sobrecarga do método **app.OnActivated**, substitua a última chamada para o método **refreshTodoItems** pelo seguinte código: 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

	Se você estiver usando um provedor de identidade diferente do Google, altere o valor passado para o método <strong>login</strong> acima para um destes: _contadamicrosoft_, _twitter_, _google_ ou _activedirectorydowindowsazure_.

    >[AZURE.NOTE]Se você tiver registrado as informações do pacote do aplicativo da Windows Store nos Serviços Móveis, deverá chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> fornecendo um valor <strong>true</strong> para o parâmetro <em>useSingleSignOn</em>. Se você não fizer isso, um prompt de logon ainda será apresentado aos usuários toda vez que o método de logon for chamado.

2. No projeto de aplicativos da Windows Store, abra o arquivo do projeto default.html e adicione o seguinte elemento de **botão** um pouco antes do elemento que define o botão **salvar**:

      	<button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. Pressione a tecla F5 para executar o aplicativo e entrar no aplicativo com seu provedor de identidade escolhido.

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

<!---HONumber=August15_HO6-->