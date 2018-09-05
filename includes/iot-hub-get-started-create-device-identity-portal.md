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
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094207"
---
## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, use o [portal do Azure](https://portal.azure.com) para criar uma identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "Registro de identidade" do [Guia do desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Use o painel **Dispositivos IoT** no portal para gerar uma ID de dispositivo e uma chave exclusivas para o dispositivo a ser usado para identificar-se no Hub IoT. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Entre no [Portal do Azure](https://portal.azure.com)

1. Selecione **Todos os recursos** e encontre seu recurso do Hub IoT.

1. Quando o recurso Hub IoT é aberto, clique na ferramenta **Dispositivos IoT** e clique em **Adicionar** na parte superior. 

    ![Criar identidade do dispositivo no portal](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Forneça um nome para o novo dispositivo, como **myDeviceId** e clique em **Salvar**. Essa ação cria uma nova identidade do dispositivo para o Hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Adicionar um novo dispositivo](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. Na lista de dispositivos, clique no dispositivo recém-criado e copie a **Cadeia de conexão – chave primária** para uso futuro.

    ![Cadeia de conexão de dispositivo](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
