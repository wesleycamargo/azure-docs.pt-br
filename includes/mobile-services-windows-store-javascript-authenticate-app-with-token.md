
O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o serviço móvel sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma melhor abordagem é armazenar em cache o token de autorização retornado pelos Serviços Móveis e tentar usá-lo antes de usar a entrada baseada no provedor.

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelos Serviços Móveis usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.

1. No arquivo de projeto default.js, substitua a função **Logon** existente pelo seguinte código:

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. Substitua a função **autenticar** existente pelo seguinte código:

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	Nesta versão do **authenticate**, o aplicativo tenta usar as credenciais armazenadas no **PasswordVault** para acessar ao serviço móvel. Uma consulta simples é enviada para verificar que o token armazenado não tenha expirado. Se um 401 for retornado, tenta-se uma entrada normal baseada no provedor. Também é realizada uma entrada normal quando não há uma credencial armazenada.

3. Reiniciar o aplicativo.

	Observe que na primeira inicialização, a entrada no provedor é requerida novamente. Porém, na segunda inicialização são usadas as credenciais armazenadas em cache e ignora-se a entrada. 

<!--HONumber=49-->