---
title: Gerar uma cadeia de caracteres de conexão do dispositivo para o Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivo, como faço para gerar uma cadeia de caracteres de conexão para o dispositivo que precisa para se conectar a um aplicativo de IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426673"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Gerar uma cadeia de caracteres de conexão de dispositivo para se conectar a um aplicativo do Azure IoT Central

Este artigo descreve como como um desenvolvedor de dispositivo, para gerar uma cadeia de caracteres de conexão para o dispositivo que precisa para se conectar a um aplicativo de IoT Central. O procedimento descrito neste artigo mostram como conectar-se rapidamente um único dispositivo usando uma assinatura de acesso compartilhado (SAS). Essa abordagem é útil quando você estiver experimentando a IoT Central ou dispositivos de teste. Para obter métodos alternativos para usar em um ambiente de produção, consulte [conectividade do dispositivo no Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Um computador de desenvolvimento com [Node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

## <a name="get-connection-information"></a>Obter informações de conexão

As etapas a seguir descrevem como obter as informações necessárias gerar uma cadeia de caracteres de conexão SAS para um dispositivo:

1. No **Explorer**, localize o dispositivo real que você deseja se conectar ao seu aplicativo:

    ![Selecione um dispositivo real](media/howto-generate-connection-string/real-devices.png)

1. Sobre o **dispositivo** página, selecione **Connect**:

    ![Selecione conectar-se](media/howto-generate-connection-string/connect.png)

1. Anote os detalhes de conexão, **ID do escopo**, **ID do dispositivo**, e **chave primária do dispositivo**, para usar as seguintes etapas:

    ![Detalhes da conexão](media/howto-generate-connection-string/device-connect.png)

    Você pode copiar os valores dessa página para salvar.

## <a name="generate-the-connection-string"></a>Gerar a cadeia de conexão

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Próximos passos

Agora que você gerou uma cadeia de caracteres de conexão para um dispositivo real para se conectar ao seu aplicativo do Azure IoT Central, aqui estão as próximas etapas sugeridas:

* [Preparar e conectar um dispositivo de kit de desenvolvimento (C)](howto-connect-devkit.md)
* [Preparar e conectar um Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Preparar e conectar um Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Preparar e conectar um dispositivo do Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Conectar-se um cliente genérico do Node. js ao seu aplicativo do Azure IoT Central](howto-connect-nodejs.md)