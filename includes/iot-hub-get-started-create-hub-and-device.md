---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371364"
---
## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. 

No menu de navegação do Hub IoT, abra a opção **Políticas de acesso compartilhado**.
Selecione a política **iothubowner** e, em seguida, copie a **Cadeia de conexão – chave primária** do Hub IoT. Para saber mais, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Você não precisará dessa cadeia de conexão de iothubowner para este tutorial de configuração. No entanto, talvez você precise dela para alguns dos tutoriais ou cenários de IoT diferentes após a conclusão dessa configuração.

   ![Obter sua cadeia de conexão do Hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrar um dispositivo no hub IoT para o dispositivo

1. No menu de navegação do Hub IoT, abra **Dispositivos IoT** e, em seguida, clique em **Adicionar** para registrar um dispositivo no Hub IoT.

   ![Adicionar um dispositivo no Dispositivo IoT de seu hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Insira uma **Identificação do Dispositivo** para o novo dispositivo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Clique em **Salvar**.
5. Após a criação do dispositivo, abra o dispositivo na lista do painel **Dispositivos IoT**.
6. Copie a **Cadeia de conexão – chave primária** para uso futuro.

   ![Obter a cadeia de conexão do dispositivo](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
