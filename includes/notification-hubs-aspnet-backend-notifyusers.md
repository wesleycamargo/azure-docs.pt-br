## Criar o projeto WebAPI

Siga as etapas abaixo para criar um novo back-end de WebAPI do ASP.NET para autenticar clientes e gerar notificações, ou modificar um back-end existente de projetos anteriores ou o tutorial [Enviar notificações de push para usuários autenticados](http://azure.microsoft.com/ documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/).

> [AZURE.NOTE] **Importante**: Antes de iniciar este tutorial, certifique-se de ter instalado a versão mais recente do Gerenciador de Pacotes NuGet. Para verificar, inicie o Visual Studio. A partir do menu **Ferramentas**, clique em **Extensões e atualizações**. Pesquise **Gerenciador de Pacotes NuGet para Visual Studio 2013**, certificando-se de ter instalada a versão 2.8.50313.46 ou posterior. Se não tiver, desinstale o Gerenciador de Pacotes NuGet e instale-o novamente.
> 
> ![][4]

> [AZURE.NOTE] Certifique-se de ter instalado o [SDK do Azure](http://azure.microsoft.com/ downloads/) do Visual Studio para a implantação do site.

1. Inicie o Visual Studio ou o Visual Studio Express.
2. No Visual Studio, clique em **Arquivo**, em seguida, clique em **Novo**, em seguida, **Projeto**, expanda **Modelos**, **Visual C#**, em seguida, clique em **Web** e **Aplicativo Web ASP.NET**, digite o nome **AppBackend**e, em seguida, clique em **OK**. 
	
	![][1]

3. Na caixa de diálogo **Novo Projeto ASP.NET**, clique em **API Web** e, por último, clique em **OK**.

	![][2]

4. Na caixa de diálogo **Configurar Site do Azure**, escolha uma assinatura, região e banco de dados a utilizar para este projeto. Em seguida, clique em **OK** para criar o projeto.

	![][5]

5. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AppBackend** e clique em **Gerenciar Pacotes NuGet**.

6. No lado esquerdo, clique em **Online**e procure **barramento de serviço** na caixa **pesquisa**.

7. Na lista de resultados, clique em **Barramento de Serviço do Microsoft Azure** e, em seguida, clique em **Instalar**. Conclua a instalação e, por fim, feche a janela do gerenciador de pacotes NuGet.

	![][14]

8. Agora, criaremos uma nova classe **Notifications.cs**. No Gerenciador de Soluções, clique com o botão direito na pasta **Modelos** e clique em **Adicionar**. Em seguida, clique em **Classe**. Depois de nomear a nova classe **Notifications.cs**, clique em **Adicionar** para gerar a classe. Esse módulo representa as diferentes notificações seguras que serão enviadas. Em uma implementação completa, as notificações são armazenadas em um banco de dados. Para simplificar, este tutorial armazena-os na memória.

	![][6]

9. Em Notifications.cs, adicione a seguinte instrução `using` no topo do arquivo:

        using Microsoft.ServiceBus.Notifications;

10. Em seguida, substitua a definição da classe `Notifications` pelo seguinte e certifique-se de substituir os dois espaços reservados pela cadeia de conexão (com acesso completo) para o hub de notificação e o nome do hub (disponível no [Portal de gerenciamento do Azure](http://manage.windowsazure.com)):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Em seguida, criaremos uma nova classe **AuthenticationTestHandler.cs**. No Gerenciador de Soluções, clique com o botão direito no projeto **AppBackend** e clique em **Adicionar**. Em seguida, clique em **Classe**. Nomeie a nova classe **AuthenticationTestHandler.cs** e clique em **Adicionar** para gerar a classe. Essa classe é utilizada para autenticar usuários utilizando *Basic Authentication*. Observe que seu aplicativo pode utilizar qualquer esquema de autenticação.

12. Em AuthenticationTestHandler.cs, adicione as seguintes instruções `using`:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. Em AuthenticationTestHandler.cs, substitua a definição da classe `AuthenticationTestHandler` pelo seguinte:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Observação de Segurança:** A classe `AuthenticationTestHandler` não fornece autenticação verdadeira. Ela é usada somente para imitar a autenticação básica e não é segura. Você deve implementar um mecanismo de autenticação seguro em seus aplicativos e serviços de produção.				

14. Adicione o seguinte código ao fim do método `Register` na classe **App_Start/WebApiConfig.cs**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Em seguida, criamos um novo controlador **RegisterController**. No Gerenciador de Soluções, clique com o botão direito na pasta **Controladores**, depois em **Adicionar**, por fim clique em **Controlador**. Clique no item **Controlador de API Web 2 -- Vazio** e, em seguida, clique em **Adicionar**. Nomeie a nova classe **RegisterController**, em seguida clique em **Adicionar** novamente para gerar o controlador.

	![][7]

	![][8]

16. Em RegisterController.cs, adicione as seguintes instruções `using`:

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Adicione o código a seguir na definição de classe `RegisterController`. Observe que, neste código, adicionamos a tag de usuário para o usuário que foi autenticado pelo manipulador. Você também pode adicionar verificações opcionais para conferir se o usuário tem direitos para registro das tags requeridas.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Crie um novo controlador **NotificationsController**, do mesmo modo como criamos **RegisterController**. Esse componente expõe um modo para o dispositivo recuperar a notificação de forma segura e fornece uma maneira para um usuário disparar um envio seguro para dispositivos. Observe que, ao enviar a notificação ao Hub de Notificação, enviamos uma notificação bruta com somente a ID da notificação (sem a mensagem em si).

19. Em NotificationsController.cs, adicione as seguintes instruções `using`:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Adicione o seguinte código dentro da definição de classe **NotificationsController** e certifique-se de comentar os trechos de código para plataformas com as quais não estiver trabalhando.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Pressione **F5** para executar o aplicativo e garantir a precisão de seu trabalho até aqui. O aplicativo deve inicializar um navegador da Web e exibir a página inicial ASP.NET. 

22. Agora, implantaremos esse aplicativo em um Site do Azure para torná-lo acessível a partir de todos os dispositivos. Clique com o botão direito do mouse no projeto **AppBackend** e selecione **Publicar**.

23. Selecione o Site do Azure como seu destino de publicação.

    ![][B15]

24. Faça logon em sua conta do Azure e selecione seu site novo ou existente.

    ![][B16]

25. Anote a propriedade **URL de destino** na guia **Conexão**. Iremos nos referir a essa URL, posteriormente neste tutorial, como seu *backend endpoint*. Clique em **Publicar**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=45--> 
