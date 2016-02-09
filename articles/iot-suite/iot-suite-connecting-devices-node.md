<properties
   pageTitle="Conectar um dispositivo usando o Node.js | Microsoft Azure"
   description="Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Conectar seu dispositivo à solução pré-configurada de monitoramento remoto do IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilar e executar a solução de exemplo node.js

1. Para clonar o repositório GitHub dos *SDKs do Microsoft Azure IoT* e instalar o *SDK de dispositivo de IoT do Microsoft Azure para Node.js* em seu ambiente de área de trabalho, siga as instruções em [Preparar seu ambiente de desenvolvimento][lnk-github-prepare].

2. Em sua cópia local do repositório [azure-iot-sdks][lnk-github-repo], copie os dois seguintes arquivos da pasta node/device/samples em uma pasta no seu dispositivo:

  - packages.json
  - remote\_monitoring.js

3. Abra o arquivo remote\_monitoring.js e procure a seguinte variável:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Substitua a **[cadeia de conexão de dispositivo do Hub IoT]** pela cadeia de conexão do seu dispositivo. Você pode encontrar os valores do seu nome de host Hub IoT, ID do dispositivo e chave do dispositivo no painel da solução de monitoramento remoto. Uma cadeia de conexão do dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

5. Se o seu nome de host do Hub IoT for **contoso** e a sua ID de dispositivo for **mydevice**, sua cadeia de conexão terá esta aparência:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

6. Salve o arquivo. Execute os seguintes comandos na pasta que contém esses arquivos:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md

<!---HONumber=AcomDC_0128_2016-->