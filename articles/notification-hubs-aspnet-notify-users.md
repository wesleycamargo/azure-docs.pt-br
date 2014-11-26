<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notificar usuários" pageTitle="Notificar usuários de seu serviço ASP.NET com Hubs de Notificação" metaKeywords="" description="Siga este tutorial para se registrar para receber notificações de seu serviço ASP.NET usando Hubs de Notificação" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notificar usuários com Hubs de Notificação" authors=""  solutions="" writer="glenga" manager="" editor=""  />

#Notificar usuários com Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/manage/services/notification-hubs/notify-users" title="Serviços Móveis">Serviços Móveis</a><a href="/pt-br/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET" class="current">ASP.NET</a>
</div> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end da API Web ASP.NET é usado para autenticar clientes e gerar notificações. Este tutorial se baseia no hub de notificação que você criou no tutorial anterior **Introdução aos Hubs de Notificação**. O código do registro de notificação é movido do cliente para o serviço de back-end. Isso garante que o registro seja concluído depois que um cliente tiver sido autenticado positivamente pelo serviço. Também significa que as credenciais do hub de notificação não são distribuídas com o aplicativo cliente. O serviço também controla as marcas solicitadas durante o registro.

Este tutorial explica as seguintes etapas básicas: 

+ [Criar um aplicativo ASP.NET com autenticação]
+ [Atualizar seu aplicativo ASP.NET para se registrar para notificações]
+ [Atualizar o aplicativo para fazer logon e solicitar o registro]

## Pré-requisitos

+ Visual Studio 2012. Você também pode usar o Visual Studio Express 2012 para Web e o Visual Studio Express 2012 para Windows 8 para criar o aplicativo ASP.NET e o aplicativo da Windows Store, respectivamente. 
+ Este tutorial se baseia no aplicativo e no hub de notificação que você criou em **Introdução aos Hubs de Notificação**.  Antes de iniciar este tutorial, você deve primeiro concluir o tutorial **Introdução aos Hubs de Notificação** ([Windows Store C#][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]). 

<div class="dev-callout"><b>Observação</b>
	<p>O projeto da API Web ASP.NET que você cria neste tutorial é executado em seu computador local. Você também pode publicar um projeto da API Web ASP.NET no Azure. Para obter mais informações, consulte <a href="/pt-br/develop/net/tutorials/rest-service-using-web-api/" target="_blank">Criar um serviço REST com acesso para dispositivos móveis usando a API Web ASP.NET e o Banco de Dados SQL</a>.</p>
</div>

<h2><a name="create-application"></a><span class="short-header">Criar um aplicativo ASP.NET</span>Criar um aplicativo ASP.NET com autenticação</h2>

Primeiro, você criará um aplicativo da API Web ASP.NET. Esse serviço de back-end autenticará clientes, registrará para notificações por push em nome de um usuário autenticado e enviará notificações.

1. No Visual Studio ou no Visual Studio Express 2012 para Web, clique em **Arquivo**, em seguida, em **Novo projeto no meu Arquivo, expanda **Modelos**, **Visual C#** e clique em **Web** e em **Aplicativo da Web do ASP.NET MVC 4**, digite o nome _NotificationService_ e clique em **OK**.

	![][0]

2. Na caixa de diálogo **Novo Projeto ASP.NET MVC**, clique em **Vazio** e clique em **OK**.

	Isso cria um projeto ASP.NET MVC.

3. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e, em seguida, em **Classe** e digite `AuthenticationTestHandler` e clique em **Adicionar**.

	![][1] 

	Isso adiciona um arquivo de código para a classe **AuthenticationTestHandler** ao projeto.

4. Abra o novo arquivo de projeto AuthenticationTestHandler.cs e substitua a definição de classe pelo seguinte código:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Security.Principal;
		using System.Text;
		using System.Web;
		
		namespace NotificationService
		{
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
		            } else return Unauthorised();
		
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
		} 

	<div class="dev-callout"><b>Observação de Segurança</b>
		<p>A classe <strong>AuthenticationTestHandler</strong> não fornece autenticação verdadeira. É usada somente para imitar a autenticação básica e retornar um princípio. O nome do usuário é necessário para criar registros do Hub de Notificação. A implementação acima não é segura. Você deve implementar um mecanismo de autenticação seguro em seus aplicativos e serviços de produção.</p>
	</div>

5. Expanda a pasta **App_Start**, abra o arquivo WebApiConfig.cs e adicione a seguinte linha de código no final do método **Register**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

	Isso requer que as solicitações para o aplicativo ASP.NET contenham o cabeçalho de Autorização.

Agora criamos o aplicativo básico com um esquema de autenticação fictício para nos fornecer um nome de usuário. 

<h2><a name="register-notification"></a><span class="short-header">Registrar-se para notificações</span>Atualizar seu aplicativo ASP.NET para se registrar para notificações</h2>

A próxima etapa é adicionar a lógica de registro para hubs de notificação para o aplicativo ASP.NET criando um novo controlador de **Registro**. 

1. Faça logon no [Portal de Gerenciamento do Azure][Management Portal], clique em **Service Bus**, seu namespace, **Hubs de Notificação**, escolha seu hub de notificação e clique em **Informações de Conexão**.  

	![][6]

2. Anote o nome do seu hub de notificação e copie a cadeia de conexão para **DefaultFullSharedAccessSignature**.

	![][7]

	Você usará essa cadeia de conexão, junto com o nome do hub de notificação, para registrar-se e enviar notificações.

3. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

4. No painel esquerdo, selecione a categoria **Online**, procure `WindowsAzure.ServiceBus`, clique em **Instalar** no pacote **Service Bus do Azure** e aceite o contrato de licença. 

  	![][2]

  	Isso adiciona o assembly Microsoft.ServiceBus.dll a seu projeto.

5. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Controladores**, clique em **Adicionar**, clique em **Controlador...**, digite `RegisterController` para o **Nome do controlador**, selecione **Controlador de API vazio** e clique em **Adicionar**.

	![][3]

	Isso adiciona uma classe de controlador ao projeto. Esse controlador, quando invocado, fará o trabalho de registrar um dispositivo nos Hubs de Notificação.

6. Abra o arquivo do novo projeto RegisterController.cs e adicione as seguintes instruções **using**.

		using Microsoft.ServiceBus.Notifications;
		using Newtonsoft.Json.Linq;
		using System.Threading.Tasks;
		using System.Web;

7. Adicione o seguinte código à nova classe RegisterController: 

		// Define the Notification Hubs client.
		private NotificationHubClient hubClient;

		// Create the client in the constructor.
        public RegisterController()
        {
            var cn = "<FULL_SAS_CONNECTION_STRING>";
            hubClient = NotificationHubClient
				.CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
        }

8. Atualize o código no construtor para substituir _`<NOTIFICATION_HUB_NAME>`_ e _`<FULL_SAS_CONNECTION_STRING>`_ pelos valores de seu hub de notificação e clique em **Salvar**.

	<div class="dev-callout"><b>Observação</b>
		<p>Certifique-se de usar <strong>DefaultFullSharedAccessSignature</strong> para <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>. Essa declaração permite que a API Web crie e atualize registros.</p>
	</div>

9. Adicione o seguinte código do método Post à nova classe RegisterController:

        public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

	Esse código é invocado por uma solicitação POST e obtém as informações da plataforma e de deviceID no corpo da mensagem. Esses dados, juntamente com a ID de instalação do cabeçalho da solicitação e a ID do usuário conectado, são usados para atualizar um registro. Se um registro não existir, ele cria um novo registro. Esse registro é marcado com a ID do usuário e a ID da instalação.

10. Adicione o seguinte método sendNotification:

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

	Esse método é chamado para enviar uma notificação imediatamente quando o registro é concluído.

Em seguida, atualizaremos o aplicativo cliente que você criou quando você concluiu o tutorial **Introdução aos Hubs de Notificação**. 

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para fazer logon e solicitar o registro</h2>

O aplicativo que você criou quando concluiu o tutorial **Introdução ao Hubs de Notificação** solicita o registro diretamente no hub de notificação. Você removerá esse código de registro do aplicativo cliente e o substituirá por uma chamada para a nova API de Registro no aplicativo da API Web ASP.NET.

1. Pressione F5 para iniciar o aplicativo ASP.NET e tentar carregar a página padrão. 

	Quando o navegador for exibido, anote o nome do host do site solicitado. Você precisará dessa URL raiz ao atualizar o aplicativo cliente.

	<div class="dev-callout"><b>Observação</b>
		<p>Quando estiver usando o servidor Web do IIS local ou o Servidor de Desenvolvimento do Visual Studio, você também deverá especificar o número da porta. Observe que um erro 404 é retornado porque não implementamos uma página padrão neste aplicativo.</p>
	</div>

2. Siga as etapas em uma das seguintes versões de **Registrar o usuário atual para notificações por push usando a API Web ASP.NET**, dependendo de sua plataforma de cliente:

	+ [Windows Store C# versão][Client topic Windows Store C# version]
	+ [iOS versão][Client topic iOS version]

3. Execute o aplicativo atualizado, faça logon com o serviço usando a mesma cadeia de caracteres de nome de usuário e senha e, em seguida, verifique se a ID de registro atribuída à notificação é exibida.

	Você também receberá uma notificação por push.

	<div class="dev-callout"><b>Observação</b>
		<p>Um erro é gerado no back-end quando não há nenhum registro para uma plataforma para a qual uma notificação é solicitada para envio. Nesse caso, esse erro pode ser ignorado. Para ver como usar modelos para evitar essa situação, consulte <a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-aspnet" target="_blank">Enviar notificações entre plataformas para usuários com Hubs de Notificação</a>.</p>
	</div>

4. (Opcional) Implante o aplicativo cliente em um segundo dispositivo, e em seguida, execute o aplicativo e insira texto. 

	Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu este tutorial, considere concluir os seguintes tutoriais:

+ **Usar Hubs de Notificação para enviar notícias recentes ([Windows Store C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Esse tutorial específico da plataforma mostra como usar marcas para permitir que os usuários inscrevam-se em tipos de notificações nas quais estão interessados. 

+ **[Enviar notificações entre plataformas a usuários com Hubs de Notificação]**<br/>Esse tutorial estende o tutorial atual **Notificar usuários com Hubs de Notificação** para usar modelos específicos de plataforma para se registrar para notificações. Isso permite que você envie notificações de um único método em seu código do lado do servidor.

Para obter mais informações sobre Hubs de Notificação, consulte [Hubs de Notificação do Azure].


[Criar um aplicativo ASP.NET com autenticação]: #create-application
[Atualizar seu aplicativo ASP.NET para se registrar para notificações]: #register-notification
[Atualizar o aplicativo para fazer logon e solicitar o registro]: #update-app
[Atualizar seu aplicativo ASP.NET para enviar notificações]: #send-notifications


[0]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png
[1]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png
[2]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png
[3]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png
[6]: ./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png



[Introdução à Windows Store]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet
[Introdução ao iOS]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios
[Introdução ao Android]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-android
[Introdução à autenticação na Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introdução à autenticação no iOS]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios/
[Introdução à autenticação no Android]: /pt-br/develop/mobile/tutorials/get-started-with-users-android/
[Versão da Windows Store C# de tópico de cliente]: /pt-br/manage/services/notification-hubs/register-users-aspnet-dotnet 
[Versão do iOS de tópico de cliente]: /pt-br/manage/services/notification-hubs/howto-register-user-with-aspnet-ios 
[Visual Studio 2012 Express para Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Portal de Gerenciamento]: https://manage.windowsazure.com/

[Criar um serviço REST usando a API Web ASP.NET e o Banco de Dados SQL]: /pt-br/develop/net/tutorials/rest-service-using-web-api/
[Enviar notificações entre plataformas a usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-xplat-aspnet
[Notícias mais recentes do .NET]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Notícias mais recentes do iOS]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Hubs de Notificação do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927170.aspx

