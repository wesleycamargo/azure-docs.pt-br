---
title: "Introdução aos Hubs de Notificação do Azure para aplicativos do iOS | Microsoft Docs"
description: "Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do iOS."
services: notification-hubs
documentationcenter: ios
keywords: "notificação por push, notificações por push, notificações por push do ios"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Introdução aos Hubs de Notificação do Azure para aplicativos do iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Você criará um aplicativo do iOS em branco que recebe notificações por push usando o [APNs (Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Ao finalizar, você poderá usar seu hub de notificação para difundir notificações por push a todos os dispositivos que executam seu aplicativo.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo deste tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>pré-requisitos
Este tutorial exige o seguinte:

* [Estrutura de Mensagens do Microsoft Azure]
* Versão mais recente do [Xcode]
* Um dispositivo compatível com o iOS 10 (ou versão posterior)
* [Programa de Desenvolvedores de iOS](https://developer.apple.com/programs/) 
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações por push, você deve implantá-las e testá-las em um dispositivo iOS físico (iPhone ou iPad), em vez de usar o Simulador de iOS.
  > 
  > 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o Hub de Notificação para notificações por push do iOS
Esta seção guia você pelas etapas para criar um novo hub de notificação e a configurar a autenticação com APNS usando o certificado push **.p12** que você criou anteriormente. Se você quiser usar um hub de notificação já criado, ignore a etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Em <b>Serviços de Notificação</b>, selecione <b>Apple (APNS)</b>. Certifique-se de selecionar <b>Certificado</b>, clique no ícone de arquivo e selecione o arquivo <b>.p12</b> que você exportou anteriormente. Especifique também a senha correta.</p>

<p>Selecione o modo de <b>Área Restrita</b>, pois se trata de desenvolvimento. Use a <b>Produção</b> apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Configurar o APNS no portal do Azure][6]

&emsp;&emsp;&emsp;&emsp;![Configurar certificação do APNS no Portal do Azure][7]

Você configurou agora seu hub de notificação para funcionar com o APNS e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Conectar seu aplicativo do iOS aos Hubs de Notificação
1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .
   
    ![Xcode - aplicativo de modo de exibição único][8]
    
2. Ao definir as opções para o novo projeto, lembre-se de usar os mesmos **Nome do Produto** e **Identificador Organizacional** que você usou quando configurou o identificador do pacote no Portal do Desenvolvedor da Apple.
   
    ![Xcode - opções de projeto][11]
    
3. No Navegador de Projeto, clique no nome do projeto, clique na guia **Geral** e localize **Assinatura**. Verifique se você selecionou a equipe apropriada para sua conta de Desenvolvedor da Apple. O deverá automaticamente efetuar pull do perfil de provisionamento que você criou anteriormente com base em seu identificador de pacote.
   
    Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis da sua identidade de assinatura. Clique em **Xcode** na barra de menus, em **Preferências**, na guia **Conta**, no botão **Exibir Detalhes**, em sua identidade de assinatura e depois clique no botão Atualizar no canto inferior direito.

    ![Xcode - perfil de provisionamento][9]

4. Selecione a guia **recursos** e verifique se você habilitou as notificações por Push

    ![Xcode – funcionalidades de push][12]
   
5. Baixe a [Estrutura de Mensagens do Microsoft Azure] e descompacte o arquivo. No Xcode, clique com o botão direito do mouse no projeto e clique na opção **Adicionar Arquivos a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto do Xcode. Selecione **Opções** e verifique se a opção **Copiar itens se necessário** está selecionada e, em seguida, clique em **Adicionar**.

    ![Descompactar o SDK do Azure][10]

6. Adicione um novo arquivo de cabeçalho chamado **HubInfo.h** ao projeto. Esse arquivo conterá as constantes para o hub de notificação. Adicione as seguintes definições e substitua os espaços reservados da cadeia de caracteres literal pelo *nome do hub* e a *DefaultListenSharedAccessSignature* que você anotou anteriormente.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Abra o arquivo **AppDelegate.h** e adicione as seguintes diretivas de importação:

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. No **arquivo AppDelegate.m**, adicione o seguinte código ao método **didFinishLaunchingWithOptions** com base em sua versão do iOS. Esse código registra seu identificador de dispositivo nas APNs:

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. No mesmo arquivo, adicione os métodos a seguir:

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Esse código conecta-se ao hub de notificação usando as informações de conexão que você especificou em HubInfo.h. Esse código fornece o token do dispositivo ao hub de notificação para que o hub de notificação possa enviar notificações.

10. No mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Compile e execute o aplicativo no dispositivo para verificar se não há falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações por push de teste
Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [Portal do Azure]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>Como verificar se o aplicativo pode receber notificações por push
Para testar as notificações por push no iOS, você deve implantar o aplicativo em um dispositivo iOS físico. Não é possível enviar notificações por push da Apple com o Simulador do iOS.

1. Execute o aplicativo e verifique se o registro foi bem-sucedido e pressione **OK**.
   
    ![Teste de registro de notificação por push de aplicativo do iOS][33]
2. Em seguida, você pode enviar uma notificação por push de teste do [Portal do Azure], conforme descrito acima. 

3. A notificação por push é enviada a todos os dispositivos que estão registrados para receber as notificações do Hub de Notificação específico.
   
    ![Teste de recebimento de notificação por push de aplicativo do iOS][35]

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você enviou notificações por push a todos os seus dispositivos iOS registrados. Uma próxima etapa recomendada para seu aprendizado seria começar o tutorial [Notificar usuários nos Hubs de Notificação do Azure para iOS com o back-end do .NET]. Este guia lhe orientará durante a criação de um back-end para enviar notificações por push usando marcações. 

Se desejar segmentar os usuários por grupos de interesse, você também poderá ir para o tutorial [Usar Hubs de Notificação para enviar as últimas notícias] . 

Para obter informações gerais sobre os Hubs de Notificação, confira [Diretrizes dos Hubs de Notificação].

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Estrutura de Mensagens do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Notificar usuários nos Hubs de Notificação do Azure para iOS com o back-end do .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Usar Hubs de Notificação para enviar as últimas notícias]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com
