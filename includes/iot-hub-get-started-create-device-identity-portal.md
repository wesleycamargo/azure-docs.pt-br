---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, use o [portal do Azure][lnk-azure-portal] para criar uma identidade de dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "Registro de identidade" do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Use o painel **Dispositivos IoT** no portal para gerar uma ID de dispositivo e uma chave exclusivas para o dispositivo a ser usado para identificar-se no Hub IoT. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Verifique se você entrou no [portal do Azure][lnk-azure-portal].

1. Na barra de navegação, clique em **Todos os recursos** e localize o recurso Hub IoT.

    ![Navegar até o Hub Iot][img-find-iothub]

1. Quando o recurso Hub IoT é aberto, clique na ferramenta **Dispositivos IoT** e clique em **Adicionar** na parte superior. Forneça o nome para o novo dispositivo, como **myDeviceId** e clique em **Salvar**.

    ![Criar identidade do dispositivo no portal][img-create-device]

   Essa ação cria uma nova identidade do dispositivo para o Hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Na lista de dispositivos dos **Dispositivos IoT**, clique no dispositivo recém-criado e anote a **Chave primária da cadeia de conexão**.

    ![Cadeia de conexão de dispositivo][img-connection-string]

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

