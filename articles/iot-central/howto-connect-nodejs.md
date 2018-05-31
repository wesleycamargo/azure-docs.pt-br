---
title: Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo Node.js genérico ao aplicativo Azure IoT Central.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8666a2db051cbd4a93c3e587aeaef3e1722b1b83
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199588"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Conectar um aplicativo cliente genérico ao aplicativo Azure IoT Central (Node.js)

Este artigo descreve como um desenvolvedor de dispositivos conecta um aplicativo Node.js genérico representando um dispositivo físico ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
1. Um computador de desenvolvimento com [Node.js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

No aplicativo Azure IoT Central, é necessário ter um modelo de dispositivo com as medidas e propriedades do dispositivo definidas a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

Adicione a telemetria a seguir na página **Medidas**:

| Nome de exibição | Nome do campo  | Unidades | Min | max | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidade     | umidade    | %     | 0   | 100 | 0              |
| Pressão     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  O tipo de dados da medida de telemetria é duplo.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem, a telemetria não poderá ser exibida no aplicativo.

### <a name="state-measurements"></a>Medidas de estado

Adicione o estado a seguir na página **Medidas**:

| Nome de exibição | Nome do campo  | Valor 1 | Nome de exibição | Valor 2 | Nome de exibição |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo da ventoinha     | fanmode     | 1       | Executando      | 0       | Parado      |

> [!NOTE]
  O tipo de dados da medida de Estado é cadeia de caracteres.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem, o estado não poderá ser exibido no aplicativo.

### <a name="event-measurements"></a>Medidas de evento

Adicione o evento a seguir na página **Medidas**:

| Nome de exibição | Nome do campo  | Severity |
| ------------ | ----------- | -------- |
| Superaquecimento  | overheat    | Erro    |

> [!NOTE]
  O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="device-properties"></a>Propriedades do dispositivo

Adicione as propriedades de dispositivo a seguir na **página de propriedades**:

| Nome de exibição        | Nome do campo        | Tipo de dados |
| ------------------- | ----------------- | --------- |
| Número de série       | serialNumber      | text      |
| Fabricante do dispositivo | manufacturer      | text      |

Insira os nomes de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem, o aplicativo não poderá mostrar o valor da propriedade.

### <a name="settings"></a>Configurações

Adicione as configurações de **número** a seguir na **página de configurações**:

| Nome de exibição    | Nome do campo     | Unidades | Decimais | Min | max  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocidade da ventoinha       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Temperatura definida | setTemperature | F     | 0        | 20  | 200  | 80      |

Insira o nome de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem, o dispositivo não poderá receber o valor de configuração.

### <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo criado e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md)

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente que implementa o dispositivo real que você adicionou ao aplicativo.

1. Crie uma pasta chamada `connected-air-conditioner-adv` no computador. Navegue até essa pasta no ambiente de linha de comando.

1. Para inicializar o projeto Node.js, execute os comandos a seguir:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Crie um arquivo chamado **connectedAirConditionerAdv.js** na pasta `connected-air-conditioner-adv`.

1. Adicione as `require` instruções no início do arquivo **connectedAirConditionerAdv.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Adicione as declarações de variáveis a seguir ao arquivo:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Atualize o espaço reservado `{your device connection string}` com a cadeia de conexão do dispositivo. Você copiou esse valor da página de detalhes da conexão quando adicionou o dispositivo real. Neste exemplo, inicializamos `targetTemperature` em zero e, opcionalmente, é possível fazer a leitura atual do dispositivo ou valor do dispositivo gêmeo. 

1. Para enviar medidas de telemetria, estado e evento ao aplicativo Azure IoT Central, adicione a função a seguir ao arquivo:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Para enviar as propriedades de dispositivo ao aplicativo Azure IoT Central, adicione a função a seguir ao arquivo:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir as configurações que o dispositivo responde, adicione a definição a seguir:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Para identificar configurações atualizadas do aplicativo Azure IoT Central, adicione o seguinte ao arquivo:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte para concluir a conexão ao Azure IoT Central e associe as funções no código cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Execute o aplicativo Node.js

Execute o comando a seguir no ambiente de linha de comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Como um operador no aplicativo Azure IoT Central, para o dispositivo real, é possível:

* Exibir a telemetria na página **Medidas**:

    ![Exibir telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Exibir os valores da propriedade de dispositivo enviados pelo dispositivo na página **Propriedades**.

    ![Exibir propriedades de dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Configure a velocidade da ventoinha e a temperatura de destino na página **Configurações**.

    ![Configurar velocidade da ventoinha](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um cliente Node.js genérico ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:
* [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
