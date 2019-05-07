---
title: Conectar um dispositivo de SensorTile.box ao seu aplicativo do Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivo, saiba como conectar um dispositivo SensorTile.box ao seu aplicativo do Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160899"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Conectar o dispositivo de SensorTile.box ao seu aplicativo do Azure IoT Central

Este artigo descreve como como um desenvolvedor de dispositivo, para se conectar a um dispositivo SensorTile.box ao seu aplicativo do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa ter os seguintes recursos:

* Um dispositivo SensorTile.box, consulte [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box) para obter mais informações.
* O aplicativo de ST BLE Sensor instalado em seu dispositivo Android, você pode [baixá-lo aqui] (https://play.google.com/store/apps/details?id=com.st.bluems). Para obter mais informações, visite: [ST BLE Sensor] (http://www.st.com/stblesensor)
* Um aplicativo do Azure IoT Central criado a partir de **DevKits** modelo de aplicativo. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
* Adicione a **SensorTile.box** modelo de dispositivo em seu aplicativo de Central da IoT visitando o **modelos de dispositivo** página, clicando em **+ novo**e selecionando o **SensorTile** modelo.

### <a name="get-your-device-connection-details"></a>Obter detalhes de conexão de seu dispositivo

Em seu aplicativo do Azure IoT Central, adicionar um dispositivo real do **SensorTile.box** modelo de dispositivo e anote os detalhes de conexão do dispositivo: **Definir o escopo de ID, ID do dispositivo e chave primária**:

1. Adicione um dispositivo de Device Explorer. Selecione **+ Novo > Real** para adicionar um dispositivo real.

    * Insira um minúsculo **ID do dispositivo**, ou usar o sugerido **ID do dispositivo**.
    * Insira um **nome do dispositivo**, ou use o nome sugerido

    ![Adicionar dispositivo](media/howto-connect-sensortile/real-device.png)

1. Para obter detalhes de conexão de dispositivo **ID do escopo**, **ID do dispositivo**, e **Primary key**, selecione **Connect** na página do dispositivo.

    ![Detalhes da conexão](media/howto-connect-sensortile/connect-device.png)

1. Anote os detalhes de conexão. Você está temporariamente desconectado da internet quando você prepara seu dispositivo do Kit de desenvolvimento na próxima etapa.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurar o SensorTile.box com o aplicativo móvel

Nesta seção, você aprenderá como enviar por push o firmware do aplicativo no dispositivo e enviar os dados do dispositivo para IoT Central por meio do aplicativo móvel ST BLE Sensor por meio de conectividade de baixa energia BLE (Bluetooth).
1. Abra o aplicativo ST BLE Sensor e pressione a **criar um novo aplicativo** botão.

    ![Criar aplicativo](media/howto-connect-sensortile/create-app.png)

1. Selecione o **indicador** aplicativo.
1. Pressione o botão carregar.

    ![Upload de indicador](media/howto-connect-sensortile/barometer-upload.png)

1. Pressione o botão play associado com seu SensorTile.box.
1. Quando o processo for concluído, o SensorTile.box transmitirá a temperatura, pressão e umidade por BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Conectar-se a SensorTile.box para a nuvem

Nesta seção, você aprenderá como conectar-se a SensorTile.box para o aplicativo móvel e conectar o aplicativo móvel para a nuvem.
1. Usando o menu à esquerda, selecione o **log de nuvem** botão.

    ![Registro em log de nuvem](media/howto-connect-sensortile/cloud-logging.png)

1. Selecione **do Azure IoT Central** como o provedor de nuvem.
1. Inserir a ID do dispositivo e a ID do escopo que foram observadas anteriormente.

    ![Credenciais](media/howto-connect-sensortile/credentials.png)

1. Selecione o **chave do aplicativo** botão de opção.
1. Clique em **Connect** e selecione os dados de telemetria que você deseja carregar.
1. Após alguns segundos, os dados aparecerão no painel do aplicativo IoT Central.

## <a name="sensortilebox-device-template-details"></a>Detalhes do modelo de dispositivo SensorTile.box

Um aplicativo criado a partir do modelo de dispositivo de SensorTile.box com as seguintes características:

### <a name="telemetry"></a>Telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressão       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um SensorTile.box ao seu aplicativo do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo de IoT.
