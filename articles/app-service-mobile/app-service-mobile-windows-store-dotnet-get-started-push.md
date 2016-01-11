<properties 
	pageTitle="Adicione notificações de push para seu aplicativo universal do Windows Runtime 8.1 | Aplicativos móveis do Azure" 
	description="Saiba como usar os Aplicativos Móveis do Serviço de Aplicativo do Azure e Hubs de Notificação do Azure para enviar notificações por push para seu aplicativo do Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Adicionar notificações de push para seu aplicativo universal do Windows Runtime 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tópico mostra como enviar notificações por push para um aplicativo universal do Windows Runtime 8.1 usando os Aplicativos móveis do serviço de aplicativo do Azure e os Hubs de notificação do Azure. Nesse cenário, quando um novo item é adicionado, o back-end do Aplicativo móvel envia uma notificação por push para todos os aplicativos do Windows registrados com o Serviço de Notificação do Windows (WNS).

Este tutorial se baseia no guia de início rápido do Aplicativo móvel do Serviço de Aplicativo. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial de início rápido [Criar um aplicativo do Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Conclua o [tutorial de início rápido](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Criar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registrar seu aplicativo para notificações por push

Antes de poder enviar notificações por push para aplicativos do Windows a partir do Azure, você deve enviar seu aplicativo para a Windows Store. Você pode configurar seu projeto de servidor para integrar com o WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configurar o back-end para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Atualizar o servidor para enviar notificações por push

Agora que as notificações por push estão habilitadas no aplicativo, você deve atualizar seu back-end do aplicativo para enviar notificações por push. Use o procedimento abaixo, que corresponde ao seu tipo de projeto de back-end, um [back-end .NET](#dotnet) ou [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end .NET

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar Pacotes NuGet**, pesquise por Microsoft.Azure.NotificationHubs e então clique em **Instalar**. Isso instala a biblioteca de cliente de Hubs de notificação.

2. Expanda **Controladores**, abra TodoItemController.cs e adicione os elementos a seguir usando instruções:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. No método **PostTodoItem**, adicione o seguinte código após a chamada para **InsertAsync**:

	    // Get the settings for the server project.
	    HttpConfiguration config = this.Configuration;
	    MobileAppSettingsDictionary settings =
	        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
	
	    // Get the Notification Hubs credentials for the Mobile App.
	    string notificationHubName = settings.NotificationHubName;
	    string notificationHubConnection = settings
	        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
	
	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
	
	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
	
	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }
	
	Esse código informa o hub de notificação para enviar uma notificação por push após uma inserção de item nova.

4. Republicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end Node.js

1. Se você ainda não fez isso, [baixe o projeto de início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, use o [editor online no Portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Substitua o código existente no arquivo todoitem.js pelo código a seguir:

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,  
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');
	    
	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;  

	Isso envia uma notificação WNS que contém o item.text quando um novo item todo é inserido.

2. Ao editar o arquivo no seu computador local, republique o projeto do servidor.

##<a id="update-service"></a>Adicionar notificações de push para seu aplicativo

1. Abra o arquivo de projeto **App.xaml.cs** compartilhado e adicione as seguintes instruções `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. No mesmo arquivo, adicione a seguinte definição de método **InitNotificationsAsync** à classe de **aplicativo**:
    
        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Esse código recupera o ChannelURI do aplicativo de WNS e registra esse ChannelURI com seu Aplicativo Móvel do Serviço de Aplicativo.
    
3. Na parte superior do manipulador de eventos **OnLaunched** no **App.xaml.cs**, adicione o modificador **async** à definição do método e adicione a seguinte chamada ao novo método **InitNotificationsAsync**, como mostrado no seguinte exemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Isso garante que o ChannelURI de curta duração seja registrado sempre que o aplicativo for iniciado.

4. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** no aplicativo da Windows Store, em **Notificações**, defina **Compatível com notificações** para **Sim**.

    No menu **Arquivo**, clique em **Salvar Tudo**.

5. Repita a etapa anterior no projeto do aplicativo do Windows Phone Store.

Seu aplicativo agora está pronto para receber notificações do sistema.

##<a id="test"></a>Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Mais

* Os modelos fornecem a flexibilidade para enviar pushes de plataforma cruzada e pushes localizados. [Como usar o cliente gerenciado para Aplicativos Móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) mostra como registrar os modelos.
* As marcas permitem que você tenha como alvo clientes segmentados com pushes. [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) mostra como adicionar marcas à instalação de um dispositivo.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1223_2015-->