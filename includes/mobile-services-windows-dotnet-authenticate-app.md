
1. Abra o arquivo do projeto mainpage.xaml.cs e adicione a seguinte instrução using:

        using Windows.UI.Popups;

6. Adicione o trecho de código a seguir à classe MainPage:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Isso cria uma variável de membro para armazenar o usuário atual e um método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Se você estiver usando um provedor de identidade que não seja o Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor do seu provedor.

8. Substitua o método **OnNavigatedTo** existente pelo seguinte método que chama o novo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. Pressione a tecla F5 para executar o aplicativo e entrar no aplicativo com o provedor de identidade escolhido. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deveser capaz de consultar os Serviços Móveis e fazer atualizações nos dados.


