---
title: Registrar um novo dispositivo do Azure IoT Edge (portal)| Microsoft Docs
description: Use o portal do Azure para registrar um novo dispositivo de IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034193"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrar um novo dispositivo do Azure IoT Edge com portal do Azure

Antes que você possa usar os seus dispositivos IoT com o Azure IoT Edge, você precisa registrá-los com o seu Hub IoT. Quando você registrar um dispositivo, você receberá uma cadeia de caracteres de conexão que pode ser usada para configurar o seu dispositivo para cargas de trabalho Edge. 

Este artigo descreve como registrar um novo dispositivo do IoT Edge usando portal do Azure.

## <a name="prerequisites"></a>pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure. 

## <a name="create-a-device"></a>Criar um dispositivo

No portal do Azure, dispositivos de extremidade IoT são criados e gerenciados separadamente dos dispositivos que se conectar ao seu hub IoT, mas não habilitado de borda. 

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
2. Selecionar **IoT Edge** do menu.
3. Selecione **Adicionar dispositivo com IoT Edge**. 
4. Forneça um ID de dispositivo descritivo. 
5. Clique em **Salvar**. 

## <a name="view-all-devices"></a>Exibir todos os dispositivos

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página. 

## <a name="retrieve-the-connection-string"></a>Recuperar a cadeia de conexão

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT.

1. Do **IoT Edge** no portal, clique na ID de dispositivo na lista de dispositivos Edge. 
2. Copie o valor de **cadeia de caracteres de Conexão, chave primária** ou **cadeia de caracteres de Conexão — chave secundária**. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar módulos em um dispositivo com o portal do Azure](how-to-deploy-modules-portal.md)