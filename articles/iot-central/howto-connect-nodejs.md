---
title: Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo Node.js genérico ao aplicativo Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 9d97b14d52a33d6879ee92b38993f4526823c4a0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963976"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Conectar um aplicativo cliente genérico ao aplicativo Azure IoT Central (Node.js)

Este artigo descreve como um desenvolvedor de dispositivos conecta um aplicativo Node.js genérico representando um dispositivo físico ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte [criar início rápido de aplicativo](quick-deploy-iot-central.md).
1. Um computador de desenvolvimento com [Node.js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

No aplicativo Azure IoT Central, é necessário ter um modelo de dispositivo com as medidas e propriedades do dispositivo definidas a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

Adicione a telemetria a seguir na página **Medidas**:

| Nome de exibição | Nome do campo  | Unidades | Min | max | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidade     | umidade    | %     | 0   | 100 | 0              |
| Pressão     | pressão    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  O tipo de dados da medição de telemetria é um número de ponto flutuante.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem aos nomes das propriedades no código de dispositivo correspondente, a telemetria não poderá ser exibida no aplicativo.

### <a name="state-measurements"></a>Medidas de estado

Adicione o estado a seguir na página **Medidas**:

| Nome de exibição | Nome do campo  | Valor 1 | Nome de exibição | Valor 2 | Nome de exibição |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo ventilação     | fanmode     | 1       | Executando      | 0       | Parado      |

> [!NOTE]
  O tipo de dados da medida State é string.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem aos nomes das propriedades no código de dispositivo correspondente, o estado não poderá ser exibido no aplicativo.

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

Insira os nomes de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem aos nomes das propriedades no código de dispositivo correspondente, o aplicativo não poderá mostrar o valor da propriedade do dispositivo.

### <a name="settings"></a>Configurações

Adicione as configurações de **número** a seguir na **página de configurações**:

| Nome de exibição    | Nome do campo     | Unidades | Decimais | Min | max  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocidade da ventoinha       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Temperatura definida | setTemperature | F     | 0        | 20  | 200  | 80      |

Insira o nome de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes dos campos não corresponderem aos nomes das propriedades no código de dispositivo correspondente, o dispositivo não poderá receber o valor da configuração.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo criado e anote a cadeia de conexão do dispositivo. Para obter instruções passo a passo sobre como conectar um aplicativo Node.js ao IoT Central, consulte [Gerar cadeia de conexão para dispositivo real a partir do aplicativo](tutorial-add-device.md#generate-connection-string-for-real-device-from-application) e [Preparar o código do cliente](tutorial-add-device.md#prepare-the-client-code) em Tutoriais> Adicionar um dispositivo.

### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente que implementa o dispositivo real que você adicionou ao aplicativo. Aqui, o aplicativo Node.js representa o dispositivo físico real. 

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

  > [!NOTE]
  > O Azure IoT Central fez a transição para o uso do DPS (Serviço de Provisionamento de Dispositivos no Hub IoT) para todas as conexões de dispositivos, siga estas instruções para [obter a sequência de conexão de dispositivo](concepts-connectivity.md#getting-device-connection-string) e continue com o restante do tutorial. Para obter mais ajuda, você também pode encontrar um conjunto detalhado de instruções [preparar o código do cliente](tutorial-add-device.md#prepare-the-client-code) nos tutoriais > Adicionar um dispositivo.


  Atualizar o espaço reservado `{your device connection string}` com a cadeia de caracteres de conexão do dispositivo. Neste exemplo, inicializamos `targetTemperature` em zero e, opcionalmente, é possível fazer a leitura atual do dispositivo ou valor do dispositivo gêmeo. 

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

* Exibir os valores da propriedade de dispositivo enviados pelo dispositivo na página **Propriedades**. Os blocos de propriedades do dispositivo serão atualizados se a conexão for bem-sucedida. 

    ![Exibir propriedades do dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Configure a velocidade da ventoinha e a temperatura de destino na página **Configurações**. Os valores das configurações serão sincronizados se a conexão for bem-sucedida. 

    ![Configurar velocidade da ventoinha](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um cliente Node.js genérico ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:
* [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
