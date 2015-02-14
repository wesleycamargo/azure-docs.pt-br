

1. Abra o arquivo de projeto default.js e na sobrecarga do método **app.OnActivated**, substitua a última chamada para o método **refreshTodoItems** pelo seguinte código: 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    Isso cria uma variável de membro para armazenar o usuário atual e um método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para o método <strong>logon</strong> acima para um dos seguintes: _microsoftaccount_, _twitter_, _google_, ou _windowsazureactivedirectory_.

    >[AZURE.NOTE]Se você registrou as informações do pacote de aplicativos da Windows Store nos Serviços Móveis, deverá chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> fornecendo um valor de <strong>true</strong> para o parâmetro <em>useSingleSignOn</em>. Se você não fizer isso, um prompt de logon ainda será apresentado aos usuários toda vez que o método de logon for chamado.

2. Pressione a tecla F5 para executar o aplicativo e entrar no aplicativo com seu provedor de identidade escolhido. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.<!--HONumber=42-->
