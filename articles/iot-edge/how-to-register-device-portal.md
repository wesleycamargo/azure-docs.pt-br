---
title: Registrar um novo dispositivo no portal do Azure – Azure IoT Edge | Microsoft Docs
description: Use o portal do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126302"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrar um novo dispositivo do Azure IoT Edge com portal do Azure

Antes que você possa usar os seus dispositivos IoT com o Azure IoT Edge, você precisa registrá-los com o seu Hub IoT. Quando você registrar um dispositivo, você receberá uma cadeia de caracteres de conexão que pode ser usada para configurar o seu dispositivo para cargas de trabalho Edge.

Este artigo descreve como registrar um novo dispositivo do IoT Edge usando portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.

## <a name="create-a-device"></a>Criar um dispositivo

No portal do Azure, dispositivos de extremidade IoT são criados e gerenciados separadamente dos dispositivos que se conectar ao seu hub IoT, mas não habilitado de borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
2. Selecionar **IoT Edge** do menu.
3. Selecione **Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para a geração automática as chaves de autenticação e para conectar o novo dispositivo ao seu hub.
5. Clique em **Salvar**.

## <a name="view-all-devices"></a>Exibir todos os dispositivos

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

## <a name="retrieve-the-connection-string"></a>Recuperar a cadeia de conexão

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT.

1. Do **IoT Edge** no portal, clique na ID de dispositivo na lista de dispositivos Edge.
2. Copie o valor da **Cadeia de conexão (chave primária)** ou **Cadeia de conexão (chave secundária)**.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar módulos em um dispositivo com o portal do Azure](how-to-deploy-modules-portal.md)
