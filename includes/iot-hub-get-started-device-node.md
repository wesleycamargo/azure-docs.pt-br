## Criar um aplicativo de dispositivo simulado
Nesta seção, você criará um aplicativo do console do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Crie uma nova pasta vazia chamada **simulateddevice**. Na pasta **simulateddevice**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device-amqp**:
   
    ```
    npm install azure-iot-device-amqp --save
    ```
3. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.js** na pasta **simulateddevice**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js**:
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. Adicione uma variável **connectionString** e use-a para criar um cliente do dispositivo. Substitua **{youriothubname}** por seu nome de hub IoT e **{yourdeviceid}** e **{yourdevicekey}** pelos valores de dispositivo gerados na seção *Criar uma identidade de dispositivo*:
   
    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. Adicione a seguinte função para exibir a saída do aplicativo:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Crie um retorno de chamada e use a função **setInterval** para enviar uma nova mensagem ao Hub IoT a cada segundo:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```
8. Abra a conexão com o Hub IoT e comece a enviar mensagens:
   
    ```
    client.open(connectCallback);
    ```
9. Salve e feche o arquivo **SimulatedDevice.js**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.
> 
> 

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->