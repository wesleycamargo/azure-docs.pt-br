---
title: Configurar o Windows Push Notification Service nos Hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações de serviço de notificação por Push do Windows para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488168"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de Windows Push Notification Service (WNS) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de serviço de notificação do Windows (WNS) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o serviço de notificação por Push do Windows (WNS)

O procedimento a seguir fornece as etapas para definir configurações do Windows Push Notification Service (WNS) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Windows (WNS)** no menu à esquerda.
2. Insira valores para **SID do Pacote** e **Chave de Segurança**.
3. Clique em **Salvar**.

   ![Captura de tela que mostra as caixas SID do pacote e a chave de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para enviar notificações para aplicativos da plataforma Universal do Windows usando os Hubs de notificação do Azure e o Windows Push Notification Service (WNS), consulte [enviar notificações para aplicativos UWP usando o Azure Os Hubs de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


