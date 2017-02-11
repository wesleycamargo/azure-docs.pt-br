---
title: Conectar um dispositivo usando o Node.js | Microsoft Docs
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Compilar e executar a solução de exemplo node.js
1. Para clonar o repositório GitHub do *SDK de IoT do Microsoft Azure* e instalá-lo em seu ambiente de área de trabalho, siga as instruções em [Preparar seu ambiente de desenvolvimento][lnk-github-prepare].
2. Em sua cópia local do repositório [azure-iot-sdk-node][lnk-github-repo], copie os dois seguintes arquivos da pasta device/samples para uma pasta no seu dispositivo:
   
   * package.json
   * remote_monitoring.js
3. Abra o arquivo remote_monitoring.js e procure a seguinte variável:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. Substitua a **[cadeia de conexão de dispositivo do Hub IoT]** pela cadeia de conexão do seu dispositivo. Você pode encontrar os valores do seu nome de host Hub IoT, ID do dispositivo e chave do dispositivo no painel da solução de monitoramento remoto. Uma cadeia de conexão do dispositivo tem o seguinte formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Se o nome de host do Hub IoT for **contoso** e a ID de dispositivo for **mydevice**, a cadeia de conexão terá esta aparência:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. Salve o arquivo. Execute os seguintes comandos no prompt de comando na pasta que contém os arquivos para instalar os pacotes necessários e, em seguida, execute o aplicativo de exemplo:
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


