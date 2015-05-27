
O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o Serviço de Aplicativo sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo Serviço de Aplicativo e tentar usá-lo antes de usar um logon baseado em provedor.

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelos Serviços de Aplicativos usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.

1. No arquivo de projeto MainPage.xaml.cs e adicione as seguintes instruções **using**:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Substitua o método **AuthenticateAsync** pelo seguinte código:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";
              
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            while (credential == null)
            {
                try
                {
                    // Try to get an existing credential from the vault.
                    credential = vault.FindAllByResource(provider).FirstOrDefault();
                }
                catch (Exception)
                {
                    // When there is no matching resource an error occurs, which we ignore.
                }

                if (credential != null)
                {
                    // Create a user from the stored credentials.
                    user = new MobileServiceUser(credential.UserName);
                    credential.RetrievePassword();
                    user.MobileServiceAuthenticationToken = credential.Password;
                    
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {                        
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            vault.Remove(credential);
                            credential = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);                        

                        // Create and store the user credentials.
                        credential = new PasswordCredential(provider,
                            user.UserId, user.MobileServiceAuthenticationToken);
                        vault.Add(credential);
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

	Nesta versão do **AuthenticateAsync**, o aplicativo tenta usar as credenciais armazenadas no **PasswordVault** para acessar o Aplicativo Móvel. Uma consulta simples é enviada para verificar que o token armazenado não tenha expirado. Se um 401 for retornado, tenta-se um registro normal baseado no provedor. Também é realizado um registro normal quando não há uma credencial armazenada.

	>[AZURE.NOTE]Este aplicativo testa tokens expirados durante o logon, mas a expiração do token pode ocorrer após a autenticação quando o aplicativo estiver em uso. Para solucionar como lidar com erros de autorização relacionados a tokens expirados, consulte a postagem [Armazenagem em cache e manipulação de tokens expirados no SDK gerenciado pelos Serviços Móveis do Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).

3. Reiniciar o aplicativo.

	Observe que na primeira inicialização, o registro com o provedor é requerido novamente. Porém, na segunda inicialização são usadas as credenciais armazenadas em cache e o registro é desviado.
<!--HONumber=54-->