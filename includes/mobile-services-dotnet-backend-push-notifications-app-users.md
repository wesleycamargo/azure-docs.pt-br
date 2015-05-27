
1. No Gerenciador de Soluções no Visual Studio, expanda a pasta App_Start e abra o arquivo do projeto WebApiConfig.cs.

2. Adicionar a seguinte linha de código para o método Register após a definição **ConfigOptions**:

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	Isso impõe a autenticação do usuário antes de se registrar para notificações por push.

2. Clique com o botão direito do mouse em projeto, clique em **Adicionar** e, em seguida, clique em **Classe...**.

3. Nomeie a nova classe vazia `PushRegistrationHandler` e, em seguida, clique em **Adicionar**.

4. Na parte superior da página do código, adicione as seguintes instruções **using**:

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. Substitua a classe **PushRegistrationHandler** existente por este código:
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	O método **Register** é chamado durante o registro. Isso permite que você adicione uma marca ao registro que seja o ID do usuário conectado. As marcas fornecidas são validadas para impedir que um usuário se registre para outra ID de usuário. Quando uma notificação é enviada para esse usuário, ela é recebida por este e qualquer outro dispositivo registrado pelo usuário.

6. Expanda a pasta Controladores, abra o arquivo do projeto TodoItemController.cs, localize o método **PostTodoItem** e substitua a linha do código que chama o **SendAsync** com o seguinte código:

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. Publicar novamente o projeto do serviço móvel.

Agora, o serviço usa a marca de ID do usuário para enviar notificações por push (com o texto do item inserido) para todos os registros criados pelo usuário conectado.
 
<!--HONumber=54-->