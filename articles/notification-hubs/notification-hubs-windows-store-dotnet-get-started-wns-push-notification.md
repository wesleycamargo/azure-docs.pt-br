---
title: "Introdução aos Hubs de Notificação do Azure para aplicativos da Plataforma Universal do Windows | Microsoft Docs"
description: "Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo da Plataforma Universal do Windows."
services: notification-hubs
documentationcenter: windows
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: c09621d1152aafbe15039130f6ca24082dc5bd21
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>Introdução aos Hubs de Notificação para aplicativos da Plataforma Universal do Windows

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este artigo mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo da UWP (Plataforma Universal do Windows).

Neste artigo, você cria um aplicativo da Windows Store em branco que recebe notificações por push usando o serviço WNS (Notificação por Push do Windows). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que estão executando seu aplicativo.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Você pode encontrar o código completo deste tutorial [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>pré-requisitos
Este tutorial exige o seguinte:

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) ou posterior
* [Ferramentas de desenvolvimento de aplicativos UWP instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Uma conta ativa do Azure   
    Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para saber mais, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).
* Uma conta ativa da Windows Store

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do UWP.

## <a name="register-your-app-for-the-windows-store"></a>Registrar seu aplicativo para a Windows Store
Para enviar notificações por push para aplicativos da UWP, associe seu aplicativo à Windows Store. Em seguida, configure seu Hub de Notificação para se integrar ao WNS.

1. Se você não tiver registrado seu aplicativo, navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/overview), entre com sua conta da Microsoft e selecione **Criar um novo aplicativo**.

2. Digite um nome para seu aplicativo e selecione **Reservar nome do aplicativo**. Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio, crie um novo projeto de aplicativos da Loja no Visual C# usando o modelo **Aplicativo em Branco** do UWP e selecione **OK**.

4. Aceite os padrões para as versões mínima e de destino da plataforma.

5. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, selecione **Armazenar** e selecione **Associar aplicativo à Store**.  
    O assistente **Associar seu aplicativo à Windows Store** é exibido.

6. No assistente, entre com sua conta da Microsoft.

7. Selecione o aplicativo que você registrou na etapa 2, selecione **Avançar**e selecione **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

8. Novamente na página [Centro de Desenvolvimento do Windows](http://dev.windows.com/overview) de seu novo aplicativo, selecione **Serviços**, **Notificações por push** e **WNS/MPNS**.

9. Selecione **Nova Notificação**.

10. Selecione o modelo **Em Branco (Notificação do sistema)** e selecione **OK**.

11. Insira uma mensagem de **Nome** e **Contexto** Visual da notificação e selecione **Salvar como rascunho**.

12. Vá para o [Portal de Registro de Aplicativo](http://apps.dev.microsoft.com) e entre.

13. Selecione o nome do aplicativo. Nas configurações da plataforma **Windows Store**, anote a senha do **Segredo do Aplicativo** e o **SID (Identificador de segurança) do pacote**.

    >[!WARNING]
    >O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.

## <a name="configure-your-notification-hub"></a>Configurar seu Hub de Notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Em <b>Notification Services</b>, selecione <b>Windows (WNS)</b> e insira a senha secreta do aplicativo na caixa <b>Chave de Segurança</b>. Na caixa <b>SID do Pacote</b>, digite o valor que você obteve do WNS na seção anterior e selecione <b>Salvar</b>.</p>
</li>
</ol>

![As caixas SID de Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O hub de notificação agora está configurado para trabalhar com WNS. Você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação
1. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet**.  
    A janela **Gerenciar Pacotes NuGet** é aberta.

2. Na caixa de pesquisa, digite **WindowsAzure.Messaging.Managed**, selecione **Instalar**e aceite os termos de uso.
   
    ![A janela Gerenciar Pacotes NuGet][20]
   
    Essa ação baixa, instala e adiciona uma referência à biblioteca de mensagens do Azure para Windows usando o [pacote NuGet WindowsAzure.Messaging.Managed](http://nuget.org/packages/WindowsAzure.Messaging).

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

6. Para executar o aplicativo, selecione a tecla **F5**. Uma caixa de diálogo que contém a chave do registro é exibida.

Seu aplicativo agora está pronto para receber notificações do sistema.

## <a name="send-notifications"></a>Enviar notificações
Você pode testar rapidamente o recebimento de notificações em seu aplicativo enviando notificações no [portal do Azure](https://portal.azure.com/). Use o botão **Envio de Teste** no hub de notificação, conforme mostrado na imagem abaixo:

![O painel Envio de Teste](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

As notificações por push normalmente são enviadas em um serviço de back-end, como Serviços Móveis ou ASP.NET, usando uma biblioteca compatível. Se uma biblioteca não está disponível para o back-end, você também pode usar a API REST diretamente para enviar mensagens de notificação. 

Este tutorial demonstra como testar seu aplicativo cliente enviando notificações que usam o SDK do .NET para hubs de notificação em um aplicativo de console em vez de um serviço back-end. Recomendamos o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários] como a próxima etapa de envio de notificações de back-end do ASP.NET. No entanto, você pode enviar notificações usando as seguintes abordagens:

* **Interface REST**: você pode dar suporte à notificação em qualquer plataforma de back-end usando a [Interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK do .NET dos Hubs de Notificação do Microsoft Azure**: no Gerenciador de Pacotes do NuGet para o Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : confira [Como usar os Hubs de Notificação de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Aplicativos Móveis do Azure**: para obter um exemplo de como enviar notificações de um aplicativo móvel do Azure integrado com Hubs de Notificação, confira [Adicionar notificação por push a Aplicativos Móveis](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java ou PHP**: para obter exemplos de como enviar notificações usando as APIs REST, confira:
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você envia notificações para todos os seus dispositivos Windows usando o portal ou um aplicativo de console. Como a próxima etapa, recomendamos o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Ele demonstra como enviar notificações de um back-end do ASP.NET usando marcas para direcionar usuários específicos.

Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. 

Para obter mais informações gerais sobre os Hubs de Notificação, confira [Diretrizes dos Hubs de Notificação](notification-hubs-push-notification-overview.md).

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
