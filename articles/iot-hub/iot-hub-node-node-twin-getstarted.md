---
title: Introdução aos gêmeos | Microsoft Docs
description: Este tutorial mostra como usar os gêmeos
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda

---
# <a name="tutorial:-get-started-with-device-twins-(preview)"></a>Tutorial: Introdução aos dispositivos gêmeos (visualização)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No fim deste tutorial, você terá dois aplicativos de console do Node.js:

* **AddTagsAndQuery.js**, um aplicativo Node.js que deve ser executado do back-end e que adiciona marcas e consultas aos dispositivos gêmeos.
* **TwinSimulatedDevice.js**, um aplicativo Node.js que simula um dispositivo que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do Hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para compilar tanto aplicativos de back-end quanto dispositivos.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 0.10.x ou posterior.
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, veja [Avaliação Gratuita do Azure][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você cria um aplicativo de console do Node.js que adiciona metadados de local ao gêmeo associado com **myDeviceId**. Em seguida, consulta os gêmeos armazenados no hub selecionando os dispositivos localizados nos EUA e depois aqueles que indicam uma conexão celular.

1. Crie uma nova pasta vazia denominada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **addtagsandqueryapp**, execute o seguinte comando para instalar o pacote **azure-iothub**:
   
    ```
    npm install azure-iothub@dtpreview --save
    ```
3. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.js** na pasta **addtagsandqueryapp**.
4. Adicione o seguinte código ao arquivo **AddTagsAndQuery.js** e substitua o espaço reservado **{service connection string}** pela cadeia de conexão que você copiou quando criou seu hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código anterior inicializa primeiro o objeto **Registry**, depois, recupera o gêmeo para **myDeviceId** e finalmente atualiza as marcas com as informações do local desejado.
   
    Depois de atualizar as marcas, ele chama a função **queryTwins**.
5. Adicione o seguinte código ao final do **AddTagsAndQuery.js** para implementar a função **queryTwins**:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    O código anterior executa duas consultas: a primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, e o segundo aperfeiçoa a consulta para selecionar somente os dispositivos que também estão conectados por meio de rede celular.
   
    Observe que o código anterior, quando ele cria o objeto **query**, especifica um número máximo de documentos retornados. O objeto **query** contém uma propriedade booliana **hasMoreResults** que você pode usar para invocar os métodos **nextAsTwin** várias vezes para recuperar todos os resultados. Um método chamado **next** está disponível para os resultados que não são de dispositivos gêmeos, por exemplo, resultados de consultas de agregação.
6. Execute o aplicativo com:
   
        node AddTagsAndQuery.js
   
    Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![][1]

Na próxima seção, você criará um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo
Nesta seção, você cria um aplicativo de console do Node.js que se conecta ao seu hub como **myDeviceId**, e depois atualiza seu as propriedades reportadas de seu gêmeo a fim de conter as informações indicando que ele está conectado usando uma rede de celular.

> [!NOTE]
> Neste momento, os dispositivos gêmeos podem ser acessados somente de dispositivos que se conectam ao Hub IoT usando o protocolo MQTT. Consulte o artigo do [Suporte a MQTT][lnk-devguide-mqtt] para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.
> 
> 

1. Crie uma nova pasta vazia denominada **reportconnectivity**. Na pasta **reportconnectivity**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **reportconnectivity**, execute o seguinte comando para instalar os pacotes **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```
3. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.js** na pasta **reportconnectivity**.
4. Adicione o seguinte código ao arquivo **ReportConnectivity.js** e substitua o espaço reservado **{device connection string}** pela cadeia de conexão copiada quando você criou a identidade do dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código anterior, depois de inicializar o objeto **Client**, recupera o gêmeo para **myDeviceId** e atualiza suas propriedades reportadas com as informações de conectividade.
5. Executar o aplicativo do dispositivo
   
        node ReportConnectivity.js
   
    Você deve ver a mensagem `twin state reported`.
6. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Acesse novamente a pasta **addtagsandqueryapp** e execute as consultas novamente:
   
        node AddTagsAndQuery.js
   
    Desta vez, **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![][3]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade do dispositivo no registro de identidade do hub. Você adicionou os metadados do dispositivo como marcas de um aplicativo back-end e escreveu um aplicativo de dispositivo simulado para reportar informações de conectividade do dispositivo no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando a linguagem de consulta semelhante a SQL do Hub IoT.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted],
* Configure dispositivos usando as propriedades desejadas do gêmeo com o tutorial [Usar propriedades desejadas para configurar dispositivos][lnk-twin-how-to-configure],
* controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário), com o tutorial [Usar métodos diretos][lnk-methods-tutorial].

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md


<!--HONumber=Oct16_HO2-->


