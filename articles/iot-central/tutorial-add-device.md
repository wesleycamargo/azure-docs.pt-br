---
title: Adicionar um dispositivo real a um aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, adicione um dispositivo real ao aplicativo Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 695050a46df4b208205ce394cc79db891803cfa4
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731522"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Tutorial: Adicionar um dispositivo real ao aplicativo Azure IoT Central

Este tutorial mostra como adicionar e configurar um dispositivo real para o seu aplicativo Microsoft Azure IoT Central.

Este tutorial é composto de duas partes:

1. Primeiro, como um operador, você aprenderá a adicionar e configurar um dispositivo real em seu aplicativo do Azure IoT Central. No final dessa parte, você deve recuperar uma cadeia de conexão para usar na segunda parte.
2. Em seguida, como um desenvolvedor do dispositivo, você aprenderá sobre o código em seu dispositivo real. Você pode adicionar a cadeia de conexão desde a primeira parte para o exemplo de código.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um novo dispositivo real
> * Configurar o dispositivo real
> * Obter cadeia de conexão para um dispositivo real a partir do aplicativo
> * Entender como o código do cliente é mapeado para o aplicativo
> * Configurar o código do cliente para o dispositivo real

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir pelo menos o primeiro tutorial para construtores para criar o aplicativo Azure IoT Central:

* [Definir um novo tipo de dispositivo](tutorial-define-device-type.md) (Obrigatório)
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md) (Opcional)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator.md) (Opcional)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Para adicionar um dispositivo real no seu aplicativo, você deve usar o modelo de dispositivo **Ar-condicionado conectado** que você criou no tutorial [Definir um novo tipo de dispositivo](tutorial-define-device-type.md).

1. Para adicionar um novo dispositivo como um operador escolha **Device Explorer** no menu de navegação à esquerda:

   ![Página do Device Explorer mostrando o ar-condicionado conectado](media/tutorial-add-device/explorer.png)

   O **Device Explorer** mostra o modelo de dispositivo do **Ar-condicionado conectado** e do dispositivo simulado que foi criado automaticamente quando o construtor criou o modelo de dispositivo.

2. Para começar a conectar um dispositivo real de ar-condicionado conectado, escolha **Novo**, em seguida, **Real**:

   ![Comece adicionando um novo dispositivo real de ar-condicionado conectado](media/tutorial-add-device/newreal.png)

3. Insira a ID do dispositivo (**deve estar em minúsculas**) ou use a ID do dispositivo sugerido. Você também pode inserir o nome para o novo dispositivo e escolher **Criar**.  

   ![Renomear o dispositivo](media/tutorial-add-device/rename.png)



## <a name="configure-a-real-device"></a>Configurar um dispositivo real

O dispositivo real é criado a partir do modelo de dispositivo de **Ar-condicionado conectado**. Você pode usar **Configurações** para configurar seu dispositivo e definir valores de propriedade para registrar informações sobre o seu dispositivo.

1. Na página **Configurações**, observe que o status da configuração **Definir temperatura** é **nenhuma atualização**. Ela permanece nesse estado até que o dispositivo real se conecte ao aplicativo e confirme que agiu na configuração. 

    ![Configurações informando que está sincronizando](media/tutorial-add-device/settingssyncing.png)

2. Na página **Propriedades** para seu novo dispositivo de ar-condicionado real conectado ar-condicionado, local de manutenção e a última data de manutenção são ambas propriedades editáveis do dispositivo. Os campos de versão de firmware e número de série ficam vazios, até que o dispositivo esteja conectado ao aplicativo. Estes são valores somente leitura que são enviados do dispositivo e não podem ser editados.

    ![Propriedades do dispositivo para dispositivo real](media/tutorial-add-device/setproperties1.png)

3. Você pode visualizar as páginas de **Medidas**, **Regras** e **Painel** para o seu dispositivo real.

## <a name="generate-connection-string-for-real-device-from-application"></a>Gerar cadeia de conexão para um dispositivo real a partir do aplicativo

Um desenvolvedor de dispositivo precisa inserir a *cadeia de conexão* para seu dispositivo real no código que é executado no dispositivo. A cadeia de conexão permite que o dispositivo se conecte com segurança ao seu aplicativo Azure IoT Central. Você gera a cadeia de conexão como parte da preparação do código do cliente escrito em Node.js nas próximas etapas. O aplicativo do Node.js representa o ar-condicionado real conectado. 

## <a name="prepare-the-client-code"></a>Preparar o código do cliente

O exemplo de código neste artigo é escrito em [Node.js](https://nodejs.org/) e mostra apenas o código necessário para:

* Conectar como um dispositivo ao aplicativo Azure IoT Central.
* Enviar telemetria de temperatura como um dispositivo de ar-condicionado conectado.
* Enviar propriedades do dispositivo ao seu aplicativo Azure IoT Central.
* Responder a um operador que usa a configuração **Definir temperatura**.
* Tratar o comando Echo no seu aplicativo do Azure IoT Central.


Os artigos de “Tutoriais” citados na seção [Próximas etapas](#next-steps) fornecem mais exemplos mais completos e mostram o uso de outras linguagens de programação. Para obter mais informações sobre como os dispositivos se conectam ao Azure IoT Central, consulte o artigo [Conectividade do dispositivo](concepts-connectivity.md).

As etapas a seguir mostram como preparar o exemplo em [Node.js](https://nodejs.org/):

1. Instale o [Node.js](https://nodejs.org/) versão 4.0.x ou posterior em seu computador. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

1. Crie uma pasta chamada `connectedairconditioner` no computador.

1. No ambiente de linha de comando, navegue até a pasta `connectedairconditioner` que você criou.

1. Instale o gerador de chaves DPS usando o comando a seguir:
    
    ```cmd/sh
    npm i -g dps-keygen
    ```

   Saiba mais sobre a [ferramenta de linha de comando aqui](https://www.npmjs.com/package/dps-keygen).

1. Baixar e descompactar (Windows) a ferramenta de dps_cstr do [GitHub](https://github.com/Azure/dps-keygen/archive/master.zip

)

    Make sure you choose the one that matches your platform. For example, on Windows the dps_cstr.exe and the dll files should be now available in your folder. 

1. A cadeia de conexão para uma instância de dispositivo em seu aplicativo é gerada a partir de informações do dispositivo fornecidas pelo IoT Central.

   Volte para o portal de IoT Central. Na tela do dispositivo para o seu ar-condicionado real conectado, escolha **Conectar**.

   ![Página do dispositivo mostrando o link de informações de conexão](media/tutorial-add-device/connectionlink.png)


1. Na página de Conexão do dispositivo, copie e cole a ID do escopo, a ID do dispositivo e a Chave primária em um editor de texto e salve. Você pode usar esses valores na próxima etapa.

   ![Detalhes da conexão](media/tutorial-add-device/device-connect.PNG)

1. Retorne para o ambiente de linha de comando e gerar a cadeia de conexão executando:

   ```cmd/sh
   dps_cstr <scope_id> <device_id> <Primary Key>
   ```
   
   Copie a saída e salve em um novo arquivo (por exemplo, connection.txt).

1. Para inicializar o projeto Node.js, execute os comandos a seguir aceitando todos os valores padrão:

   ```cmd/sh
    npm init
   ```

1. Para instalar os pacotes necessários execute o comando a seguir:

   ```cmd/sh
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

1. Usando um editor de texto, crie um arquivo chamado **ConnectedAirConditioner.js** na pasta `connectedairconditioner`.

1. Adicione as seguintes instruções `require` no início do arquivo **ConnectedAirConditioner.js**:

   ```javascript
   'use strict';

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
   > Você atualiza o espaço reservado `{your device connection string}` em uma etapa posterior. 

1. Salve as alterações feitas até agora, mas mantenha o arquivo aberto.

## <a name="understand-how-client-code-maps-to-the-application"></a>Entender como o código do cliente é mapeado para o aplicativo

Na seção anterior, você criou um esqueleto de projeto em Node.js para um aplicativo que se conecta ao seu aplicativo Azure IoT Central. Nesta seção, você adiciona o código para:

* Conectar-se a um aplicativo Azure IoT Central.
* Enviar telemetria ao seu aplicativo Azure IoT Central.
* Enviar propriedades do dispositivo ao seu aplicativo Azure IoT Central.
* Receber configurações do seu aplicativo Azure IoT Central.
* Tratar o comando Echo no seu aplicativo do Azure IoT Central.


1. Para enviar telemetria de temperatura para o seu aplicativo Azure IoT Central, adicione o seguinte código ao arquivo **ConnectedAirConditioner.js**:

   ```javascript
   // Send device telemetry.
   function sendTelemetry() {
     var temperature = targetTemperature + (Math.random() * 15);
     var data = JSON.stringify({ temperature: temperature });
     var message = new Message(data);
     client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
       (err ? `; error: ${err.toString()}` : '') +
       (res ? `; status: ${res.constructor.name}` : '')));
   }
   ```

   O nome do campo no JSON que você enviar deve corresponder ao nome do campo especificado para a telemetria de temperatura no modelo de dispositivo. Neste exemplo, o nome do campo é **temperatura**.


1. Para enviar as propriedades do dispositivo, como **firmwareVersion** e **serialNumber**, adicione a seguinte definição:

   ```javascript
   // Send device properties
   function sendDeviceProperties(twin) {
     var properties = {
       firmwareVersion: "9.75",
       serialNumber: "10001"
     };
     twin.properties.reported.update(properties, (errorMessage) => 
       console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
   }
   ```

1. Para definir as configurações compatíveis com o dispositivo, tais como **setTemperature** adicione a definição a seguir:

   ```javascript
   // Add any settings your device supports
   // mapped to a function that is called when the setting is changed.
   var settings = {
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

1. Para lidar com as configurações enviadas ao Azure IoT Central, adicione a função a seguir que localiza e executa o código de dispositivo apropriado:

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

    Esta função:

    * Observa o Azure IoT Central enviando uma propriedade desejada.
    * Localiza a função apropriada a ser chamada para tratar a alteração de configuração.
    * Envia uma confirmação de volta para o seu aplicativo Azure IoT Central.

1. Para responder a um comando, como **echo** do seu aplicativo do Azure IoT Central, adicione a seguinte definição:

   ```javascript
   // Respond to the echo command
   function onCommandEcho(request, response) {
     // Display console info
     console.log(' * Echo command received');
     // Respond
     response.send(10, 'Success', function (errorMessage) {});
   }
   ```

1. Adicione o seguinte código para concluir a conexão ao Azure IoT Central e associe as funções no código cliente:

   ```javascript
   // Handle device connection to Azure IoT Central.
   var connectCallback = (err) => {
     if (err) {
       console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
     } else {
       console.log('Device successfully connected to Azure IoT Central');
       // Send telemetry measurements to Azure IoT Central every 1 second.
       setInterval(sendTelemetry, 1000);
       // Setup device command callbacks
       client.onDeviceMethod('echo', onCommandEcho);
       // Get device twin from Azure IoT Central.
       client.getTwin((err, twin) => {
         if (err) {
           console.log(`Error getting device twin: ${err.toString()}`);
         } else {
           // Send device properties once on device start up
           sendDeviceProperties(twin);
           // Apply device settings and handle changes to device settings.
           handleSettings(twin);
         }
       });
     }
   };

   client.open(connectCallback);
   ```

1. Salve as alterações feitas até agora, mas mantenha o arquivo aberto.

## <a name="configure-client-code-for-the-real-device"></a>Configurar o código do cliente para o dispositivo real

<!-- Add the connection string to the sample code, build, and run --> Para configurar o código do cliente para se conectar ao seu aplicativo Azure IoT Central, você precisa adicionar a cadeia de conexão para seu dispositivo real que você anotou anteriormente neste tutorial.

1. No arquivo **ConnectedAirConditioner.js**, localize a seguinte linha de código:

   ```javascript
   var connectionString = '{your device connection string}';
   ```

1. Substitua `{your device connection string}` pela cadeia de conexão do dispositivo real. Você salvou a cadeia de conexão anteriormente em um editor de texto.

1. Salve as alterações no arquivo **ConnectedAirConditioner.js**.

1. Para executar o exemplo, execute o seguinte comando no seu ambiente de linha de comando:

   ```cmd/sh
   node ConnectedAirConditioner.js
   ```

   > [!NOTE]
   > Verifique se você está na pasta `connectedairconditioner` ao executar esse comando.

1. O aplicativo imprime a saída para o console:

   ![Saída do aplicativo cliente](media/tutorial-add-device/output.png)

1. Após cerca de 30 segundos, você deve ver a telemetria na página **Medidas** do dispositivo:

   ![Telemetria real](media/tutorial-add-device/realtelemetry.png)

1. Na página **Configurações**, você poderá ver que agora a configuração está sincronizada. Quando o dispositivo conectou-se pela primeira vez, ele recebeu o valor da configuração e confirmou a alteração:

   ![Configuração sincronizada](media/tutorial-add-device/settingsynced.png)

1. Na página **Configurações**, defina a temperatura do dispositivo para **95** e escolha **Atualizar dispositivo**. O aplicativo de exemplo recebe e processa essa alteração:

   ![Receber e processar a configuração](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Há duas mensagens de “atualização de configuração”. Quando o status `pending` é enviado e quando o status `completed` é enviado.

1. Na página **Medidas** você pode ver que o dispositivo está enviando valores mais altos de temperatura:

    ![A telemetria de temperatura agora está mais alta](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="nextstepaction"]
> * Adicionar um novo dispositivo real
> * Configurar o novo dispositivo
> * Obter cadeia de conexão para um dispositivo real a partir do aplicativo
> * Entender como o código do cliente é mapeado para o aplicativo
> * Configurar o código do cliente para o dispositivo real

Agora que você conectou um dispositivo real ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

Como um operador, você pode aprender como:

* [Gerenciar seus dispositivos](howto-manage-devices.md)
* [Usar conjuntos de dispositivos](howto-use-device-sets.md)
* [Criar análise personalizada](howto-create-analytics.md)

Como um desenvolvedor do dispositivo, você pode aprender como:

* [Preparar e conectar um DevKit](howto-connect-devkit.md)
* [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
* [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
* [Personalizar seu código][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
