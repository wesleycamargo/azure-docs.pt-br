---
title: Configurar o sistema de mensagens de dispositivo Android nos Hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações de sistema de mensagens de dispositivo Android (ADM) para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 0d79e53f731c20777ffa2fb57b3b8149fe4e60ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239956"
---
# <a name="configure-android-device-messaging-adm-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de sistema de mensagens de dispositivo Android (ADM) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de sistema de mensagens de dispositivo Android (ADM) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-android-device-messaging"></a>Configurar o Android Device Messaging

O procedimento a seguir fornece as etapas para definir configurações do Amazon Device Messaging (ADM) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Amazon (ADM)** no menu à esquerda.
2. Insira valores para **ID do Cliente** e **Segredo do Cliente**.
3. Clique em **Salvar**.
    
   ![Captura de tela de configurações de ADM no portal do Azure](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para enviar notificações para dispositivos Android usando os Hubs de notificação do Azure e mensagens de dispositivo Android (ADM), consulte [Introdução aos Hubs de notificação para aplicativos do Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).

