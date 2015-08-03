

As instruções abaixo se aplicam à atualização de um cliente da Windows Store, mas isso pode ser testado em qualquer uma das outras plataformas compatíveis com os Serviços Móveis do Azure.


1. No Visual Studio, abra o MainPage.xaml.cs e adicione a seguinte instrução `using` na parte superior do arquivo.
 
        using System.Net.Http;

2. Em MainPage.xaml.cs, adicione a definição de classe a seguir ao namespace, para ajudar a serializar as informações do usuário.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. No MainPage.xaml.cs, atualize o método `AuthenticateAsync`para chamar a API personalizada para que ela retorne informações adicionais sobre o usuário do AAD.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    // Change 'MobileService' to the name of your MobileServiceClient instance.
                    // Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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


4. Salve suas alterações e compile o serviço para verificar se não existem erros de sintaxe.

<!---HONumber=July15_HO4-->