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
	ms.date="08/14/2015" 
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

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, clique em **Armazenar** > **Associar Aplicativo à Store...**. 

    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. No assistente, clique em **Avançar**, entre com sua conta da Microsoft, digite um nome para seu aplicativo em **Reservar um novo nome de aplicativo** e clique em **Reservar**.

3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar** e em **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7. Repita as etapas 1 e 3 para o projeto de aplicativo da Windows Phone Store usando o mesmo registro que você criou anteriormente para o aplicativo da Windows Store.

7. Navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/pt-BR/overview), entre com sua conta da Microsoft, clique no registro do novo aplicativo em **Meus aplicativos** e expanda **Serviços** > **Notificações por push**.

8. Na página **Notificações por push**, clique em **Site dos Serviços ao Vivo** em **Serviços Móveis do Microsoft Azure**.

9. Na guia **Configurações de Aplicativo**, anote os valores de **Segredo do cliente** e **SID de Pacote**.

    ![Configuração do aplicativo no centro do desenvolvedor](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.

##Configurar o aplicativo móvel para enviar solicitações por push

1. Faça logon no [Portal do Azure], selecione **Procurar** > **Aplicativo Móvel** > seu aplicativo > **Serviços de notificação por push**.

2. No **Serviço de Notificação do Windows**, digite a **Chave de segurança** (segredo do cliente) e o **SID do pacote** que você obteve no site dos Serviços ao Vivo e clique em **Salvar**.

O back-end do Aplicativo móvel agora está configurado para funcionar com o WNS.

##<a id="update-service"></a>Atualizar o servidor para enviar notificações por push

Agora que as notificações por push estão habilitadas no aplicativo, você deve atualizar seu back-end do aplicativo para enviar notificações por push.

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **Gerenciar pacotes NuGet**, procure por `Microsoft.Azure.NotificationHubs` e clique em **Instalar**. Isso instala a biblioteca de cliente de Hubs de notificação.

3. No projeto do servidor, abra **Controladores** > **TodoItemController.cs** e adicione os seguintes elementos usando instruções:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. No método **PostTodoItem**, adicione o seguinte código após a chamada para **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
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


## <a name="publish-the-service"></a>Publicar o back-end móvel no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Adicionar notificações de push para seu aplicativo

1. No Visual Studio, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**. 

    Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

2. Pesquise o SDK cliente do Aplicativo Móvel do Serviço de Aplicativo para gerenciado e clique em **Instalar**. Selecione todos os projetos do cliente na solução e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência em todos os projetos do cliente à biblioteca de Push Móvel do Azure para Windows.

3. Abra o arquivo de projeto **App.xaml.cs** compartilhado e adicione as seguintes instruções `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. No mesmo arquivo, adicione a seguinte definição de método **InitNotificationsAsync** à classe de **aplicativo**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Esse código recupera o ChannelURI do aplicativo de WNS e registra esse ChannelURI com seu Aplicativo Móvel do Serviço de Aplicativo.
    
5. Na parte superior do manipulador de eventos **OnLaunched** no **App.xaml.cs**, adicione o modificador **async** à definição do método e adicione a seguinte chamada ao novo método **InitNotificationsAsync**, como mostrado no seguinte exemplo:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Isso garante que o ChannelURI de curta duração seja registrado sempre que o aplicativo for iniciado.

6. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** no aplicativo da Windows Store, em **Notificações**, defina **Compatível com notificações** para **Sim**.

    No menu **Arquivo**, clique em **Salvar Tudo**.

7. Repita a etapa anterior no projeto do aplicativo do Windows Phone Store.

Seu aplicativo agora está pronto para receber notificações do sistema.

##<a id="test"></a>Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

<!-- Anchors. -->

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO2-->