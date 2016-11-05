---
title: Uso de métodos diretos | Microsoft Docs
description: Este tutorial mostra como usar métodos diretos
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy

---
# <a name="tutorial:-use-direct-methods"></a>Tutorial: Uso de métodos diretos
## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Os tutoriais anteriores ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade básica de mensagem do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT. O Hub IoT também oferece a capacidade de invocar métodos não duráveis em dispositivos da nuvem. Os métodos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada HTTP no sentido de que é bem sucedida ou falha imediatamente (após um tempo limite especificado pelo usuário) para permitir que o usuário saiba o status da chamada. [Invocar um método direto em um dispositivo][lnk-devguide-methods] descreve métodos em mais detalhes e oferece orientação sobre quando usar métodos em vez de mensagens da nuvem para o dispositivo.

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.
* Criar um dispositivo simulado que tem um método direto que pode ser chamado pela nuvem.
* Criar um aplicativo de console que chama um método direto no dispositivo simulado por meio do Hub IoT.

> [!NOTE]
> Neste momento, os métodos diretos estão acessíveis somente de dispositivos que se conectam ao Hub IoT usando o protocolo MQTT. Confira o artigo do [suporte a MQTT][lnk-devguide-mqtt] para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.
> 
> 

Ao fim deste tutorial, você terá dois aplicativos de console do Node.js:

* **CallMethodOnDevice.js**, que chama um método no dispositivo simulado e exibe a resposta.
* **SimulatedDevice.js**, que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e responde ao método chamado pela nuvem.

> [!NOTE]
> O artigo [SDKs do Hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para compilar ambos os aplicativos para execução em dispositivos e no back-end da solução.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 0.10.x ou posterior.
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, confira [Avaliação Gratuita do Azure][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Node.js que responde a um método chamado pela nuvem.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```
3. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.js** na pasta **simulateddevice**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adicione uma variável **connectionString** e use-a para criar um cliente do dispositivo. Substitua **{cadeia de conexão do dispositivo}** pela cadeia de conexão gerada na seção *Criar uma identidade de dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adicione a seguinte função para implementar o método no dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra a conexão com o Hub IoT e inicialize o ouvinte do método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Salve e feche o arquivo **SimulatedDevice.js** .

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (tais como tentar conexão novamente), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].
> 
> 

## <a name="call-a-method-on-a-device"></a>Chamar um método em um dispositivo
Nesta seção, você cria um aplicativo de console do Node.js que chama um método no dispositivo simulado e, em seguida, exibe a resposta.

1. Crie uma nova pasta vazia denominada **callmethodondevice**. Na pasta **callmethodondevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **callmethodondevice**, execute o seguinte comando para instalar o pacote **azure-iothub**:
   
    ```
    npm install azure-iothub@dtpreview --save
    ```
3. Usando um editor de texto, crie um arquivo **CallMethodOnDevice.js** na pasta **callmethodondevice**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Crie o cliente para abrir a conexão a seu Hub IoT.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Adicione a seguinte função para invocar o método de dispositivo e imprimir a resposta do dispositivo no console:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Salve e feche o arquivo **CallMethodOnDevice.js**.

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando, na pasta **simulateddevice** , execute o seguinte comando para iniciar a escuta de chamadas de método de seu Hub IoT:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Em um prompt de comando na pasta **callmethodondevice**, execute o seguinte comando para iniciar o monitoramento de seu Hub IoT:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Você verá o dispositivo reagir ao método imprimindo a mensagem e o aplicativo que chamou o método exibindo a resposta do dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade do dispositivo no registro de identidade do hub. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para reagir aos métodos invocados pela nuvem. Você também criou um aplicativo que invoca métodos no dispositivo e exibe a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao Hub IoT]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Para saber como estender sua solução IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendamento e difusão de trabalhos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Como processar mensagens de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


