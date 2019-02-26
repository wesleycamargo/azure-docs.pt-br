---
title: Configurar um hub de notificação do Azure com as configurações do PNS | Microsoft Docs
description: Neste Início Rápido, você aprenderá a definir um hub de notificação no portal do Azure com as configurações do PNS (Sistema de Notificação de Plataforma).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313888"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Configurar um hub de notificação do Azure com as configurações do Sistema de Notificação de Plataforma no portal do Azure 
Os Hubs de Notificação do Azure fornecem um mecanismo de push expansível e fácil de usar que permite que você envie notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu etc.) de qualquer back-end (nuvem ou local). Para obter mais informações sobre o serviço, confira [O que são os Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md).

[Crie um hub de notificação do Azure usando o portal do Azure](create-notification-hub-portal.md), caso ainda não tenha feito isso. Neste Início Rápido, você aprenderá a definir um hub de notificação no portal do Azure com as configurações do PNS (Sistema de Notificação de Plataforma).

## <a name="apple-push-notification-service-apns"></a>Serviço de notificação por push da Apple (APNS)
1. Na página **Hub de Notificação** no portal do Azure, selecione **Apple (APNs)** em **Configurações** no menu esquerdo.
2. Se você selecionar **Certificado**, realize as seguintes ações:
    1. Selecione o **ícone de arquivo** e o arquivo **.p12** a ser carregado. 
    2. Especifique a **senha**.
    3. Selecione o modo **Sandbox**. Use a **Produção** apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.

        ![Configurar certificação do APNS no Portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Se você selecionar **Token**, siga estas etapas: 
    1. Insira os valores de **identificação da chave**, **ID do pacote**, **ID da equipe** e **token**.
    2. Selecione o modo **Sandbox**. Use a **Produção** apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.

        ![Configurar um token APNs no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Para obter um tutorial completo sobre como enviar notificações por push para dispositivos iOS usando os Hubs de Notificação do Azure e o APNs (Apple Push Notification Service), confira [este tutorial](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google FCM (Firebase Cloud Messaging)
1. Na página **Hub de Notificação** no portal do Azure, selecione **Google (GCM/FCM)** em **Configurações** no menu esquerdo. 
2. Cole a **chave do servidor** do projeto do FCM salva anteriormente. 
3. Selecione **Salvar** na barra de ferramentas. 

    ![Hubs de Notificação do Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Você verá uma mensagem nos alertas indicando que os hubs de notificação foram atualizados com êxito. O botão **Salvar** está desabilitado. 

Para obter um tutorial completo sobre como enviar notificações por push para dispositivos Android usando os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging, confira [este tutorial](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>WNS (Serviço de Notificação por Push do Windows)
1. Na página **Hub de Notificação** no portal do Azure, selecione **Windows (WNS)** em **Configurações** no menu esquerdo.
2. Insira valores para **SID do Pacote** e **Chave de Segurança**.
3. Selecione **Salvar** na barra de ferramentas.

    ![As caixas SID de Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Para obter um tutorial completo sobre como enviar notificações por push para um aplicativo UWP (Plataforma Universal do Windows) em execução em um dispositivo Windows, confira [este tutorial](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone – Serviço de Notificação por Push da Microsoft
1. Na página **Hub de Notificação** no portal do Azure, selecione **Windows Phone (MPNS)** em **Configurações**.
2. Caso deseje habilitar um push não autenticado, selecione **Habilitar push não autenticado** e selecione **Salvar** na barra de ferramentas.

    ![Portal do Azure – habilitar notificações por push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Caso deseje usar o push **autenticado**, siga estas etapas:
    1. Selecione **Carregar Certificado** na barra de ferramentas.
    2. Selecione o **ícone de arquivo** e selecione o arquivo de certificado.
    3. Insira a **senha** do certificado. 
    4. Selecione **OK** para fechar a página **Carregar Certificado**. 
    5. Na página **Windows Phone (MPNS)**, selecione **Salvar** na barra de ferramentas.

Para obter um tutorial completo sobre como enviar notificações por push para um aplicativo do Windows Phone 8 usando o MPNS (Serviço de Notificação por Push da Microsoft), confira [este tutorial](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>ADM (Amazon Device Messaging)
1. Na página **Hub de Notificação** no portal do Azure, selecione **Amazon (ADM)** em **Configurações** no menu esquerdo.
2. Insira valores para **ID do cliente** e **Segredo do cliente**.
3. Selecione **Salvar** na barra de ferramentas.
    
    ![Hubs de Notificação do Azure – configurações do ADM](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Para obter um tutorial completo sobre como usar as notificações por push dos Hubs de Notificação do Azure em um aplicativo do Kindle, confira [este tutorial](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Na página **Hub de Notificação** no portal do Azure, selecione **Baidu (Android China)** em **Configurações** no menu esquerdo. 
2. Insira a **chave de API** obtida do console do Baidu no projeto de push da nuvem Baidu. 
3. Insira a **chave secreta** obtida do console do Baidu no projeto de push da nuvem Baidu. 
4. Selecione **Salvar** na barra de ferramentas. 

    ![Hubs de Notificação do Azure – Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Você verá uma mensagem nos alertas indicando que os hubs de notificação foram atualizados com êxito. O botão **Salvar** está desabilitado. 

Para obter um tutorial completo sobre como enviar notificações por push usando os Hubs de Notificação do Azure e o push da nuvem Baidu, confira [este tutorial](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Próximas etapas
Neste Início Rápido, você aprendeu a configurar Sistemas de Notificação de Plataforma diferentes para um hub de notificação no portal do Azure. 

Para obter instruções completas passo a passo sobre como enviar notificações para essas diferentes plataformas, confira os tutoriais na seção **Tutoriais**.

- [Enviar notificações por push para dispositivos iOS usando os Hubs de Notificação do Azure e o APNs (Apple Push Notification Service)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Enviar notificações por push para dispositivos Android usando os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Enviar notificações por push para um aplicativo UWP (Plataforma Universal do Windows) em execução em um dispositivo Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Enviar notificações por push para um aplicativo do Windows Phone 8 usando o MPNS (Serviço de Notificação por Push da Microsoft)](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Enviar notificações por push para um aplicativo do Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Enviar notificações por push usando os Hubs de Notificação do Azure e o push da nuvem Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
