---
title: Configurar o Google Firebase mensagens nos Hubs de notificação do Azure na nuvem | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com as configurações do Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239195"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações do Google Firebase Cloud Messaging (FCM) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de Google Firebase Cloud Messaging (FCM) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configure Google Firebase Cloud Messaging (FCM)

O procedimento a seguir fornece as etapas para definir configurações do Google Firebase Cloud Messaging (FCM) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Google (GCM/FCM)** no menu à esquerda. 
2. Cole a **chave de API** do projeto do FCM salva anteriormente. 
3. Clique em **Salvar**. 

   ![Captura de tela que mostra como configurar os Hubs de Notificação para Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Próximos passos
Para obter um tutorial com instruções passo a passo para enviar notificações para dispositivos Android usando os Hubs de notificação do Azure e o Google Firebase Cloud Messaging, consulte [notificações por Push para dispositivos Android usando os Hubs de notificação e FCM do Google ](notification-hubs-android-push-notification-google-fcm-get-started.md).

