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
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346827"
---
## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. 

No menu de navegação do Hub IoT, abra a opção **Políticas de acesso compartilhado**. Selecione a política **iothubowner** e, em seguida, copie a **Cadeia de conexão – chave primária** do Hub IoT. Para saber mais, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Você não precisa da string de conexão do proprietário de casa para este tutorial de configuração. No entanto, talvez você precise dela para alguns dos tutoriais ou cenários de IoT diferentes após a conclusão dessa configuração.

   ![Obter sua cadeia de conexão do Hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>Registre seu dispositivo no hub da IoT

1. No menu de navegação do Hub IoT, abra **Dispositivos IoT** e, em seguida, clique em **Adicionar** para registrar um dispositivo no Hub IoT.

   ![Adicionar um dispositivo no Dispositivo IoT de seu hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Insira uma **Identificação do Dispositivo** para o novo dispositivo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. Clique em **Salvar**.

4. Após a criação do dispositivo, abra o dispositivo na lista do painel **Dispositivos IoT**.

5. Copie a **Cadeia de conexão – chave primária** para uso futuro.

   ![Obter a cadeia de conexão do dispositivo](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
