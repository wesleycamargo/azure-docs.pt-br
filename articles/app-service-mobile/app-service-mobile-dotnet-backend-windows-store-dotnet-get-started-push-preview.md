<properties 
	pageTitle="Adicionar notificações por push ao seu aplicativo Windows Universal com o Serviço de Aplicativo do Azure" 
	description="Saiba como usar o Serviço de Aplicativo do Azure para enviar notificações por push para seu aplicativo Windows Universal." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Adicionar notificações por push ao seu aplicativo da Windows Store

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

Este tópico mostra como enviar notificações por push para um aplicativo Windows Universal de um back-end .NET usando o Serviço de Aplicativo do Azure. Quando concluir, você enviará notificações por push do back-end .NET para todos os aplicativos Windows universais registrados na inserção do registro.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Configurar](#configure)
3. [Atualizar o serviço para enviar notificações por push](#update-service)
4. [Adicionar notificações por push ao seu aplicativo](#add-push)
5. [Testar notificações por push no seu aplicativo](#test)

Este tutorial se baseia no guia de início rápido do Aplicativo móvel do Serviço de Aplicativo. Antes de iniciar este tutorial, você deve primeiro concluir uma [Introdução aos aplicativos móveis do Serviço de Aplicativo].

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Comunidade do Visual Studio 2013</a>.

##<a id="register"></a>Registrar seu aplicativo para notificações por push

Para enviar notificações por push para aplicativos Windows Universal com o Serviço de Aplicativo do Azure, você deve enviar seu aplicativo à Windows Store. Em seguida, você deve configurar as credenciais do serviço de notificação por push do aplicativo móvel para integração com o WNS.

1. Se você não tiver registrado seu aplicativo, navegue até <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Enviar uma página de aplicativo</a> no Centro de Desenvolvimento para aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

    ![][0]

2. Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

    ![][1]

    Isso cria um novo registro da Windows Store para seu aplicativo.

4. No Gerenciador de Soluções, clique com o botão direito no projeto do aplicativo da Windows Store, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.

    ![][3]

    O assistente **Associar seu aplicativo à Windows Store** é exibido.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Clique no aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

    ![][4]

    Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7. (Opcional) Repita as etapas 4-6 para o projeto do aplicativo do Windows Phone.

7. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.

    ![][5]

8. Na página **Serviços**, clique em **Site do Live Services** em **Serviços Móveis do Microsoft Azure**.

    ![][17]

9. Na guia **Configurações de aplicativo**, anote os valores de**Segredo do cliente** e **Identificador de Segurança de Pacote (SID)**.

    ![][6]

    > [AZURE.NOTE]**Observação de segurança** O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.

##<a id="configure"></a>Configurar o aplicativo móvel para enviar solicitações por push

1. Faça logon no [Portal de Visualização do Azure], selecione **Procurar**, **Aplicativo Móvel** e, em seguida, clique no seu aplicativo nos serviços de Notificação por Push.

2. No Serviço de Notificação do Windows, insira seu **Segredo do cliente** e **SDI (Identificador de segurança) do pacote** e salve.

Seu aplicativo móvel de Serviço de Aplicativo agora está configurado para funcionar com o WNS.

<!-- URLs. -->
[Portal de Visualização do Azure]: https://portal.azure.com/

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

Agora que as notificações por push estão habilitadas no aplicativo, você deve atualizar seu back-end do aplicativo para enviar notificações por push.

1. No Visual Studio, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**.

2. Pesquise por **Microsoft.Azure.NotificationHubs** e clique em **Instalar** para todos os projetos na solução.

3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do back-end móvel. Abra o TodoItemController.cs. Na parte superior do arquivo, adicione as seguintes instruções `using`:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. Adicione o seguinte fragmento ao método `PostTodoItem` após a chamada **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    Esse código informa o hub de notificação associado a esse aplicativo móvel para enviar uma notificação por push após uma inserção de item de tarefa.


## <a name="publish-the-service"></a>Publicar o back-end móvel no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Adicionar notificações por push ao seu aplicativo

1. No Visual Studio, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**. 

    Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

2. Pesquise o SDK cliente do Aplicativo Móvel do Serviço de Aplicativo para gerenciado e clique em **Instalar**. Selecione todos os projetos na solução e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência em todos os projetos à biblioteca de Push Móvel do Azure para Windows.

3. Abra o arquivo de projeto **App.xaml.cs** e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    No projeto universal, este arquivo está localizado na pasta `<project_name>.Shared` ￼￼￼.

4. No mesmo arquivo, adicione a seguinte definição de método **InitNotificationsAsync** à classe de **aplicativo**:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Esse código recupera o ChannelURI do aplicativo de WNS e registra esse ChannelURI com seu Aplicativo Móvel do Serviço de Aplicativo.
    
5. Na parte superior do manipulador de eventos **OnLaunched** no **App.xaml.cs**, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Isso garante que o ChannelURI de curta duração seja registrado sempre que o aplicativo for iniciado.

6. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** no aplicativo da Windows Store, em **Notificações**, defina **Compatível com notificações** para **Sim**.

    No menu **Arquivo**, clique em **Salvar Tudo**.

7. (Opcional) Repita a etapa anterior no projeto do aplicativo do Windows Phone Store.

8. Pressione a tecla **F5** para executar os aplicativos.

Seu aplicativo agora está pronto para receber notificações do sistema.

##<a id="test"></a>Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=July15_HO3-->