---
title: Criar um Hub IoT usando Kit de Ferramentas do Azure IoT para VS Code | Microsoft Docs
description: Como usar Kit de Ferramentas do Azure IoT para VS Code para criar um Hub IoT.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 5097d7e1f6a0b9e94919ccc8731702206c0a1568
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047226"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Criar um Hub IoT usando o Kit de Ferramentas do Azure IoT para Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra como usar o [Kit de Ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) para criar um Hub IoT do Azure. 

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Kit de Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. No Visual Studio Code, abra a exibição do **Explorer**.

2. Na parte inferior do Explorer, expanda a seção **Dispositivos do Hub IoT**. 

   ![Expanda Dispositivos do Azure Hub IoT](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, passe o mouse sobre o cabeçalho. 

4. Escolha **Criar Hub IoT**.

5. Um pop-up aparecerá no canto inferior direito para permitir que você entre no Azure pela primeira vez.

6. Selecione a assinatura do Azure. 

7. Selecione o grupo de recursos.

8. Selecione o local.

9. Selecione o tipo de preço.

10. Insira um nome exclusivo globalmente para o Hub IoT.

11. Aguarde alguns minutos até que o Hub IoT seja criado.

## <a name="next-steps"></a>Próximas etapas

Agora você implantou um Hub IoT usando o Kit de Ferramentas de IoT do Azure IoT para Visual Studio Code. Para explorar mais, confira os artigos a seguir:

* [Use a extensão do Kit de Ferramentas do Azure IoT para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o Hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Use a extensão do Kit de Ferramentas do Azure IoT para Visual Studio Code para o gerenciamento do dispositivo de Hub IoT do Azure](iot-hub-device-management-iot-toolkit.md)

* [Veja a página Wiki do Kit de Ferramentas de IoT do Azure](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
