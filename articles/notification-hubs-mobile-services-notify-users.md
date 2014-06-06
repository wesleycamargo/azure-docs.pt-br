<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notificar usuários" pageTitle="Notificar usuários sobre seu serviço móvel com Hubs de Notificação" metaKeywords="" description="Siga este tutorial para registrar-se para receber notificações de seu serviço móvel usando os Hubs de Notificação" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notificar usuários com Hubs de Notificação" authors=""  solutions="" writer="glenga" manager="" editor=""  />
# <a name="getting-started"> </a>Notificar usuários com Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/manage/services/notification-hubs/notify-users" title="Serviços Móveis" class="current">Serviços Móveis</a><a href="/pt-br/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET">ASP.NET</a>
</div> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end do Serviços Móveis do Azure é usado para autenticar clientes e gerar notificações. Este tutorial se baseia no hub de notificação que você criou no tutorial anterior **Introdução aos Hubs de Notificação**. O código do registro de notificação é movido do cliente para o serviço de back-end. Isso garante que o registro seja concluído depois que um cliente tiver sido autenticado positivamente pelo serviço. Também significa que as credenciais do hub de notificação não são distribuídas com o aplicativo cliente. O serviço também controla as marcas solicitadas durante o registro.

Este tutorial explica as seguintes etapas básicas: 

+ [Atualizar seu serviço móvel para registrar-se para notificações]
+ [Atualizar o aplicativo para fazer logon e solicitar o registro]
+ [Atualizar seu serviço móvel para enviar notificações]

## Pré-requisitos

Antes de iniciar este tutorial, você deve primeiro concluir os seguintes tutoriais:

+ **Introdução aos Hubs de Notificação** ([Windows Store C#][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]). 

+ **Introdução à autenticação nos Serviços Móveis** ([Windows Store C#][Get started auth Windows Store]/[iOS][Get started auth iOS]/[Android][Get started auth Android]

Este tutorial se baseia no aplicativo e no hub de notificação que você criou em **Introdução aos Hubs de Notificação**. Ele também utiliza o serviço móvel autenticado que você configurou na **Introdução à autenticação nos Serviços Móveis**. 

<div class="dev-callout"><b>Observação</b>
	<p>Por padrão, o tutorial <strong>Introdução à autenticação nos Serviços Móveis</strong> usa a autenticação do Facebook. Você não pode usar a autenticação da Conta da Microsoft neste tutorial, porque dois aplicativos da Windows Store não podem compartilhar um único registro do Live Connect. Para usar a autenticação da Conta da Microsoft, o serviço móvel e o hub de notificação devem ser registrados no mesmo aplicativo no Live Connect.</p>
</div>

<h2><a name="register-notification"></a><span class="short-header">Registrar-se para notificações</span>Atualizar seu serviço móvel para se registrar para notificações</h2>

Como o registro de notificação deve ser concluído apenas depois que um cliente tiver sido autenticado positivamente pelo serviço, o registro é executado por uma API personalizada definida no serviço móvel. Essa API personalizada é chamada por um cliente autenticado para solicitar registro da notificação. Nesta seção, você irá atualizar o serviço móvel autenticado que você definiu quando concluiu o tutorial **Introdução à autenticação nos Serviços Móveis**. 

1. Faça logon no [Portal de Gerenciamento do Azure][Management Portal], clique em **Service Bus**, seu namespace, **Hubs de Notificação**, escolha seu hub de notificação e clique em **Informações de Conexão**.  

	![][6]

2. Anote o nome do seu hub de notificação e copie a cadeia de conexão para **DefaultFullSharedAccessSignature**.

	![][7]

	Você usará essa cadeia de conexão, junto com o nome do hub de notificação, para registrar-se e enviar notificações.

2. Ainda no Portal de Gerenciamento, clique em **Serviços Móveis** e clique em seu aplicativo.

   	![][0]

2. Clique na guia **API** e, em seguida, clique em **Criar uma API personalizada**.

   	![][1]

   	Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3. Digite <em>register_notifications</em> em **Nome da API**, selecione **Somente Usuários Autenticados** para **Permissões de POST** e, em seguida, clique no botão de seleção.

   	![][2]

  	Isso cria a API que requer que os usuários sejam autenticados antes de chamar usando o método HTTP POST. Não é necessário definir os outros métodos HTTP porque não vamos implementá-los.

4. Clique na nova entrada **register_notifications** na tabela de API.

	![][3]

5. Clique na guia **Script** e substitua o código existente pelo código a seguir:

		exports.post = function(request, response) {

			// Create a notification hub instance.
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
		
		    // Get the registration info that we need from the request. 
		    var platform = request.body.platform;
		    var userId = request.user.userId;
		    var installationId = request.header('X-ZUMO-INSTALLATION-ID');
		    
		    // Function called when registration is completed.
		    var registrationComplete = function(error, registration) {
		        if (!error) {
		            // Return the registration.
		            response.send(200, registration);
		        } else {
		            response.send(500, 'Registration failed!');
		        }
		    }
		    // Function called to log errors.
		    var logErrors = function(error) {
		        if (error) {
		            console.error(error)
		        }
		    }
		    // Get existing registrations.
		    hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
		        var firstRegistration = true;
		        if (existingRegs.length > 0) {
		             for (var i = 0; i < existingRegs.length; i++) {
		                if (firstRegistration) {
		                    // Update an existing registration.
		                    if (platform === 'win8') {
		                        existingRegs[i].ChannelUri = request.body.channelUri;                        
		                        hub.updateRegistration(existingRegs[i], registrationComplete);                        
		                    } else if (platform === 'ios') {
		                        existingRegs[i].DeviceToken = request.body.deviceToken;
		                        hub.updateRegistration(existingRegs[i], registrationComplete);
		                    } else {
		                        response.send(500, 'Unknown client.');
		                    }
		                    firstRegistration = false;
		                } else {
		                    // We shouldn't have any extra registrations; delete if we do.
		                    hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
		                }
		            }
                } else {
                    // Create a new registration.
                    if (platform === 'win8') {                
                        hub.wns.createNativeRegistration(request.body.channelUri, 
                        [userId, installationId], registrationComplete);
                    } else if (platform === 'ios') {
                        hub.apns.createNativeRegistration(request.body.deviceToken, 
                        [userId, installationId], registrationComplete);
                    } else {
                        response.send(500, 'Unknown client.');
                    }
                }
            });
        }

	Esse código obtém informações de plataforma e deviceID no corpo da mensagem. Esses dados, juntamente com a ID de instalação do cabeçalho da solicitação e a ID do usuário conectado, são usados para atualizar um registro, se existente, ou para criar um novo registro. Esse registro é marcado com a ID do usuário e a ID da instalação.

6. Atualize o script para substituir _`<NOTIFICATION_HUB_NAME>`_ e _`<FULL_SAS_CONNECTION_STRING>`_ pelos valores de seu hub de notificação e clique em **Salvar**.

	<div class="dev-callout"><b>Observação</b>
		<p>Certifique-se de usar <strong>DefaultFullSharedAccessSignature</strong> para <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>. Essa declaração permite que seu método API personalizado crie e atualize os registros.</p>
	</div>

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para fazer logon e solicitar o registro</h2>

Em seguida, você precisa atualizar o aplicativo TodoList para solicitar registro para notificação chamando a nova API personalizada.

1. Siga as etapas em uma das seguintes versões de **Registrar o usuário atual para notificações por push usando o serviço móvel**, dependendo de sua plataforma de cliente:

	+ [Windows Store C# versão][Client topic Windows Store C# version]
	+ [iOS versão][Client topic iOS version]

2. Execute o aplicativo atualizado, faça logon usando o Facebook e, em seguida, verifique se a ID do registro atribuída à notificação é exibida.

<h2><a name="send-notifications"></a><span class="short-header">Enviar notificações</span>Atualizar seu serviço móvel para enviar notificações</h2>

A etapa final é adicionar um código que envia notificações no serviço móvel. Esse código de notificação é adicionado ao script do servidor registrado ao manipulador de inserção da tabela **TodoItem**.

1. De volta ao Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

   	![][4]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][5]

   	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo seguinte código:

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}
	
	Essa ação tentará enviar notificações à marca para o usuário conectado atual em aplicativos da Windows Store e iOS.
		
4. Atualize o script para substituir _`<NOTIFICATION_HUB_NAME>`_ e _`<FULL_SAS_CONNECTION_STRING>`_ pelos valores de seu hub de notificação e clique em **Salvar**.

   	Isso registra um novo script de inserção, que usa o objeto Hubs de Notificação para enviar uma notificação por push (o texto inserido) ao canal fornecido na solicitação de inserção.

	<div class="dev-callout"><b>Observação</b>
		<p>Certifique-se de usar <strong>DefaultFullSharedAccessSignature</strong> para <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>. Essa declaração fornece a seu script de inserção acesso total, incluindo a capacidade de enviar notificações para clientes registrados.</p>
	</div>

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

Agora que as notificações estão configuradas, está na hora de testar o aplicativo inserindo dados para gerar uma notificação.

1. Execute o aplicativo. 

	Uma notificação do registro é exibida novamente na inicialização.

2. Digite o texto como antes e adicione o novo item.

	Observe que, após a inserção ser concluída, o aplicativo receberá uma notificação dos Hubs de Notificação.

	<div class="dev-callout"><b>Observação</b>
		<p>Um erro é gerado no back-end quando não há nenhum registro para uma plataforma para a qual uma notificação é solicitada para envio. Nesse caso, esse erro pode ser ignorado. Para ver como usar modelos para evitar essa situação, consulte <a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/" target="_blank">Enviar notificações entre plataformas para usuários com Hubs de Notificação</a>.</p>
	</div>

3. (Opcional) Implante o aplicativo cliente em um segundo dispositivo, e em seguida, execute o aplicativo e insira texto. 

	Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu este tutorial, considere concluir os seguintes tutoriais:

+ **Usar Hubs de Notificação para enviar notícias recentes ([Windows Store C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Esse tutorial específico da plataforma mostra como usar marcas para permitir que os usuários inscrevam-se em tipos de notificações nas quais estão interessados. 

+ **[Enviar notificações entre plataformas a usuários com Hubs de Notificação]**<br/>Esse tutorial estende o tutorial atual **Notificar usuários com Hubs de Notificação** para usar modelos específicos de plataforma para se registrar para notificações. Isso permite que você envie notificações de um único método em seu código do lado do servidor.

Para obter mais informações sobre Hubs de Notificação, consulte [Hubs de Notificação do Azure].

<!-- Anchors. -->
[Atualizar seu serviço móvel para registrar-se para notificações]: #register-notification
[Atualizar o aplicativo para fazer logon e solicitar o registro]: #update-app
[Atualizar seu serviço móvel para enviar notificações]: #send-notifications

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png
[2]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png
[3]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png
[4]: ./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png
[5]: ./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png
[6]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png

<!-- URLs. -->
[Get started Windows Store]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet
[Get started iOS]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios
[Get started Android]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-android
[Get started auth Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started auth iOS]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios/
[Get started auth Android]: /pt-br/develop/mobile/tutorials/get-started-with-users-android/
[Client topic Windows Store C# version]: /pt-br/manage/services/notification-hubs/register-users-mobile-services-dotnet 
[Client topic iOS version]: /pt-br/manage/services/notification-hubs/register-users-ios 
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[Enviar notificações entre plataformas a usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services
[Breaking news .NET]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /pt-br/manage/services/notification-hubs/breaking-news-ios
[Hubs de Notificação do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927170.aspx

