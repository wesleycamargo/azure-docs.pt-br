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
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400180"
---
## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, use o [portal do Azure](https://portal.azure.com) para criar uma identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "Registro de identidade" do [Guia do desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Use o painel **Dispositivos IoT** no portal para gerar uma ID de dispositivo e uma chave exclusivas para o dispositivo a ser usado para identificar-se no Hub IoT. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Entre no [Portal do Azure](https://portal.azure.com)

2. Selecione **Todos os recursos** e encontre seu recurso do Hub IoT.

3. Quando o recurso Hub IoT é aberto, clique na ferramenta **Dispositivos IoT** e clique em **Adicionar** na parte superior. 

    ![Criar identidade do dispositivo no portal](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. Forneça um nome para o novo dispositivo, como **myDeviceId** e clique em **Salvar**. Essa ação cria uma nova identidade do dispositivo para o Hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Adicionar um novo dispositivo](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. Na lista de dispositivos, clique no dispositivo recém-criado e copie a **Cadeia de conexão – chave primária** para uso futuro.

    ![Cadeia de conexão de dispositivo](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
