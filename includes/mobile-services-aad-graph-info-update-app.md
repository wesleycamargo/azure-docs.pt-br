

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
        }


3. No MainPage.xaml.cs, atualize o método `AuthenticateAsync` para chamar a API personalizada para que ela retorne informações adicionais sobre o usuário do AAD. 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
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
