---
title: Configurar o envio de nuvem Baidu nos Hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações de Baidu para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234246"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar as configurações de Push de nuvem Baidu para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de Push de nuvem do Baidu de mensagens para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurar o push da nuvem Baidu
O procedimento a seguir fornece as etapas para configurar as configurações de Push de nuvem Baidu para um hub de notificação:

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Baidu (Android China)** no menu à esquerda. 
2. Insira a **chave de API** obtida do console do Baidu no projeto de push da nuvem Baidu. 
3. Insira a **chave secreta** obtida do console do Baidu no projeto de push da nuvem Baidu. 
4. Clique em **Salvar**. 

    ![Captura de tela de Hubs de Notificação que mostra a configuração para notificações por push do Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para notificações por push Baidu usando os Hubs de notificação do Azure e enviar por Push de nuvem do Baidu, consulte [Introdução aos Hubs de notificação usando o Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
