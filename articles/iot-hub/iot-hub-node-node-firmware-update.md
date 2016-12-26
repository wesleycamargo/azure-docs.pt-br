---
title: "Como realizar uma atualização de firmware | Microsoft Docs"
description: "Este tutorial mostra como realizar uma atualização de firmware"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 632b9b38808e033b1fee2676b353f2649c4a282c


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>Tutorial: Como realizar uma atualização de firmware
## <a name="introduction"></a>Introdução
No tutorial [Introdução ao gerenciamento de dispositivo][lnk-dm-getstarted], você viu como usar os primitivos [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod] para reiniciar remotamente um dispositivo. Este tutorial usa os mesmos primitivos do Hub IoT, fornece orientações e mostra como fazer uma atualização de firmware simulada de ponta a ponta.  Esse padrão é usado na implementação da atualização de firmware para o dispositivo de exemplo Intel Edison.

Este tutorial mostra como:

* Criar um aplicativo de console que chama o método direto firmwareUpdate no dispositivo simulado por meio do Hub IoT.
* Crie um aplicativo do dispositivo simulado que implementa um método direto de firmwareUpdate que passa por um processo de várias etapas e que espera para baixar a imagem do firmware, baixa a imagem do firmware e, por fim, aplica a imagem de firmware.  Durante a execução de cada etapa, o dispositivo usa as propriedades relatadas para atualizar o progresso.

Ao fim deste tutorial, você terá dois aplicativos de console do Node.js:

**dmpatterns_fwupdate_service.js**, que chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e periodicamente (a cada 500 ms) exibe as propriedades relatadas atualizadas.

**dmpatterns_fwupdate_device.js**, que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto firmwareUpdate, é executado por meio de um processo de vários estados para simular uma atualização de firmware, incluindo: aguardar o download da imagem, baixar a nova imagem e aplicar a imagem.

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 0.12.x ou posterior. <br/>  [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Consulte o artigo [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md) para criar seu hub IoT e obter a cadeia de conexão.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você irá

* Criar um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem
* Disparar uma atualização de firmware simulada
* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e quando foi a última atualização de firmware concluída

1. Criar uma nova pasta vazia denominada **manageddevice**.  Na pasta **manageddevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando na pasta **manageddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um novo arquivo **dmpatterns_fwupdate_device.js** na pasta **manageddevice**.
4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e use-a para criar um cliente do dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função, que será usada para atualizar as propriedades relatadas
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. Adicione as seguintes funções, que simularão o download e aplicarão a imagem de firmware.
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Adicione a seguinte função, que atualizará o status da atualização de firmware por meio das propriedades relatadas para "aguardando para baixar".  Normalmente, os dispositivos serão informados sobre uma atualização disponível e uma política definida pelo administrador fará com que o dispositivo inicie o download e aplique a atualização.  Este é o ponto em que a lógica para habilitar essa política seria executada.  Para simplificar, estamos atrasando por 4 segundos e prosseguindo com o download da imagem de firmware. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Adicione a seguinte função, que atualizará o status da atualização de firmware por meio das propriedades relatadas para baixar a imagem do firmware.  Ele prossegue simulando um download de firmware e, finalmente, atualiza o status de atualização do firmware para informar quanto ao êxito ou falha de um download.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Adicione a seguinte função, que atualizará o status da atualização de firmware por meio das propriedades relatadas para aplicar a imagem do firmware.  Ele prossegue simulando uma aplicação da imagem de firmware e, finalmente, atualiza o status de atualização do firmware para informar quanto ao êxito ou falha de uma aplicação.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Adicione a seguinte função, que lida com o método firmwareUpdate e inicia o processo de atualização de firmware de vários estágios.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Por fim, adicione o seguinte código que se conecta ao Hub IoT como um dispositivo. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.
> 
> 

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Disparar uma atualização de firmware remoto no dispositivo usando um método direto
Nesta seção, você cria um aplicativo de console do Node.js que inicia uma atualização de firmware remoto em um dispositivo usando um método direto e usa consultas do twin do dispositivo para obter periodicamente o status da atualização do firmware ativo no dispositivo.

1. Crie uma nova pasta vazia denominada **triggerfwupdateondevice**.  Na pasta **triggerfwupdateondevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **triggerfwupdateondevice**, execute o seguinte comando para instalar o pacote **azure-iothub** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-hub --save
    ```
3. Usando um editor de texto, crie um novo arquivo **dmpatterns_getstarted_service.js** na pasta **triggerfwupdateondevice**.
4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de espaço reservado:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Adicione a seguinte função para localizar e exibir o valor da propriedade relatada firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Adicione a seguinte função para invocar o método firmwareUpdate para reiniciar o dispositivo de destino:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Por fim, adicione a seguinte função ao código para iniciar a sequência de atualização do firmware e começar a mostrar periodicamente as propriedades relatadas:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Salve e feche o arquio **dmpatterns_fwupdate_service.js**.

## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando da pasta **manageddevice**, execute o seguinte comando para iniciar a escutar o método direto de reinicialização.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. No prompt de comando da pasta **triggerfwupdateondevice**, execute o seguinte comando para disparar a reinicialização e a consulta remota para o twin do dispositivo localizar o tempo de reinicialização mais recente.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Você verá a resposta do dispositivo para o método direto no console.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um método direto para disparar uma atualização de firmware remoto em um dispositivo e usou as propriedades relatadas para compreender o andamento do processo de atualização de firmware.  

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


