---
title: Enviar notificações para aplicativos da Plataforma Universal do Windows usando Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777055"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Tutorial: Enviar notificações para aplicativos da Plataforma Universal do Windows usando Hubs de Notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, você cria um hub de notificação para enviar notificações por push para um aplicativo da Plataforma Universal do Windows (UWP). Você cria um aplicativo da Windows Store em branco que recebe notificações por push usando o serviço WNS (Notificação por Push do Windows). Em seguida, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que estão executando seu aplicativo.

> [!NOTE]
> Você pode encontrar o código completo deste tutorial [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um aplicativo na Windows Store
> * Criar um hub de notificação
> * Criar um aplicativo de exemplo do Windows
> * Enviar notificações de teste


## <a name="prerequisites"></a>pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) ou posterior.
- [Ferramentas de desenvolvimento de aplicativos UWP instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Uma conta ativa da Windows Store

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do UWP.

## <a name="create-an-app-in-windows-store"></a>Criar um aplicativo na Windows Store
Para enviar notificações por push para aplicativos da UWP, associe seu aplicativo à Windows Store. Em seguida, configure seu Hub de Notificação para se integrar ao WNS.

1. Navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/overview), entre com sua conta da Microsoft e selecione **Criar um novo aplicativo**.

    ![Botão de novo aplicativo](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Digite um nome para seu aplicativo e selecione **Reservar nome do produto**. Isso cria um novo registro da Windows Store para seu aplicativo.

    ![Armazene o nome do aplicativo](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Expanda **Gerenciamento de aplicativo**, selecione **WNS/MPNS**, selecione **WNS/MPNS** e, em seguida, selecione **Site de serviços do Live**. Entre na sua conta da Microsoft. O **Portal de Registro de Aplicativos** abre em uma nova guia. Como alternativa, você pode navegar diretamente para o [Portal de Registro de Aplicativos](http://apps.dev.microsoft.com), selecione o nome do aplicativo para obter a esta página.

    ![Página de WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Anote a senha do **Segredo do Aplicativo** e o **SID (Identificador de segurança do pacote)**.

        >[!WARNING]
        >O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>Definir as configurações WNS para o hub

1. Selecione **Windows (WNS)** na categoria **CONFIGURAÇÕES DE NOTIFICAÇÃO**. 
2. Insira valores para **SID do pacote** e **chave de segurança** que você anotou na seção anterior. 
3. Selecione **Salvar** na barra de ferramentas.

    ![As caixas SID de Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O hub de notificação agora está configurado para trabalhar com WNS. Você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

## <a name="create-a-sample-windows-app"></a>Criar um aplicativo de exemplo do Windows
1. No Visual Studio, selecione **Arquivo**, aponte para **Novo** e selecione **Projeto**. 
2. Na caixa de diálogo **Novo Projeto**, faça o seguinte: 

    1. Expanda **Visual C#**.
    2. Selecione **Windows Universal**. 
    3. Selecione **Aplicativo em Branco (Universal do Windows)**. 
    4. Insira um **nome** para o projeto. 
    5. Selecione **OK**. 

        ![Caixa de diálogo Novo Projeto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. Aceite os padrões para as versões **mínima** e de **destino** da plataforma e selecione **OK**. 
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, selecione **Armazenar** e selecione **Associar aplicativo à Store**. O assistente **Associar seu aplicativo à Windows Store** é exibido.
3. No assistente, entre com sua conta da Microsoft.
4. Selecione o aplicativo que você registrou na etapa 2, selecione **Avançar**e selecione **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
5. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet**. A janela **Gerenciar Pacotes NuGet** é aberta.
6. Na caixa de pesquisa, digite **WindowsAzure.Messaging.Managed**, selecione **Instalar**e aceite os termos de uso.
   
    ![A janela Gerenciar Pacotes NuGet][20]
   
    Essa ação baixa, instala e adiciona uma referência à biblioteca dos Hubs de Notificação do Azure para Windows usando o [pacote NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Abra o arquivo de projeto App.xaml.cs e adicione as seguintes instruções `using`: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. Em App.xaml.cs, adicione também a seguinte definição do método **InitNotificationsAsync** à classe **App**:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Esse código recupera no WNS o URI do canal para o aplicativo e registra esse URI do canal no hub de notificação.
   
    >[!NOTE]
    >* Substitua o espaço reservado **nome do hub** pelo nome do hub de notificação que aparece no portal do Azure. 
    >* Além disso, substitua o espaço reservado da cadeia de conexão com a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na página **Políticas de Acesso** do seu hub de notificação em uma seção anterior.
   > 
   > 
5. Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    Essa ação faz com que o URI do canal seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

6. Para executar o aplicativo, selecione a tecla **F5**. Uma caixa de diálogo que contém a chave do registro é exibida. Selecione **OK** para fechar a caixa de diálogo. 

    ![Registro bem-sucedido](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Seu aplicativo agora está pronto para receber notificações do sistema.

## <a name="send-test-notifications"></a>Enviar notificações de teste
Você pode testar rapidamente o recebimento de notificações em seu aplicativo enviando notificações no [portal do Azure](https://portal.azure.com/). 

1. No portal do Azure, alterne para a guia de visão geral e selecione **Envio de teste** na barra de ferramentas.     

    ![Botão de envio de teste](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Na janela **Envio de teste**, execute as seguintes ações: 
    1. Para **Plataformas**, selecione **Windows**.
    2. Para **Tipo de notificação**, selecione **Notificação do sistema**. 
    3. Selecione **Enviar**. 
    
        ![O painel Envio de Teste](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Veja o resultado da operação de envio na lista **Resultados** na parte inferior da janela. Você também pode ver uma mensagem de alerta. 

    ![Resultado da Operação de envio](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. Você verá a mensagem de notificação: **Mensagem de teste** na área de trabalho. 

    ![Mensagem de notificação](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você envia notificações para todos os seus dispositivos Windows usando o portal ou um aplicativo de console. Para saber como enviar notificações por push a dispositivos específicos, avance ao seguinte tutorial: 

> [!div class="nextstepaction"]
>[Notificações por push para dispositivos específicos](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
